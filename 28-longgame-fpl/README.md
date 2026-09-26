# 28 — LongGame

**FPL decision engine — projections, transfers, and captaincy for long-horizon managers.**

A Fantasy Premier League decision tool that projects expected points per player over a six-gameweek horizon, then uses linear programming to recommend the best XI, captain, transfers, and chip timing. Built for managers who play the long game, not the weekly reaction.

**Status:** 🟢 Live — `longgame.onrender.com`
**Stack:** Python 3.12 · FastAPI · SQLite · PuLP (CBC) · HTMX · Render
**Tags:** #optimization #linear-programming #fastapi #htmx #sports-analytics #decision-engine

## What It Does

Pulls live data from the official FPL API, builds an expected-points (xP) projection for every player across the next six gameweeks, and turns it into concrete decisions: who to start, who to captain, which transfers are worth making, and when to play chips. Every recommendation is backed by visible component maths — no black box.

It is multi-user: any manager in a mini-league can load their own team by name and get their own read, with rival intelligence layered on top (differentials, threats, and which chips opponents have already spent).

The design priority is **auditability**: the projection model computes each xP from named inputs (start probability, attack, defence, defensive-contribution), and the optimizer solves against explicit constraints. Nothing is asserted that can't be traced to a number.

## System Components

| Component | Description |
|---|---|
| Ingest | FPL API → SQLite; weekly snapshots of players, teams, fixtures, squads, and mini-league rivals |
| Projection model | xP per player per gameweek over a 6-GW horizon — fixture-adjusted, home/away, with start-probability and minutes-based regression |
| Optimizer | PuLP linear program — best XI, captain, transfer verdict (roll vs −4 hit valued over the horizon), best squad from scratch |
| Decision layer | Chip-timing advisor, price-change watch, deadline countdown, replacement-priority ranking |
| Rival layer | League differentials, threats, elite-manager ownership, and chip availability across the mini-league |
| Interface | Server-rendered dashboard with HTMX for live in-place what-if scenarios |
| External data | LiveFPL (price-change and elite-ownership feeds) |

## Architecture

FPL API → SQLite (weekly snapshots)
→ projection model (xP per player × 6 GWs, cached per data epoch)
→ optimizer (PuLP): best XI · captain · transfers · squad
→ decision + rival layers
→ HTMX dashboard (live what-if: swaps, subs, captain, chips — stacked)


## Key Design Decisions

- **Code computes, model is auditable** — every xP decomposes into visible components (start probability, attack, defence, defensive-contribution); no hidden scoring.
- **Linear programming for lineup decisions** — best XI, formation, and transfers are solved as constrained optimizations, so recommendations are always legal (valid formation, ≤3 per club, within budget) by construction.
- **Transfer verdict valued over the horizon, not the week** — a −4 hit is only recommended when it beats the best free move by a set margin over six gameweeks, encoding a long-game discipline rather than weekly chasing.
- **Minutes-based regression** — small-sample per-90 rates are pulled toward positional priors, so a rotation player with one fluky return doesn't out-project an established starter.
- **Stacked what-if scenarios** — swaps, subs, captaincy, and chips compound in a single scenario the user builds move by move, with cumulative budget tracking, then reset.
- **Multi-user from public data** — any league manager loads by name with zero setup; exact selling prices are a bonus for the authenticated owner, with a `now_cost` fallback for everyone else.

## Validation

- Projections validated against real gameweek outcomes — within ~6 points of actual across test managers on a ~55-point week.
- Optimizer constraints proven on live squad data (affordability, legal formation, ≤3/club) before trusting recommendations.

## Rebuild-From-Prompt Protocol

Stack required:
- Python 3.12 + FastAPI
- SQLite
- PuLP (with the bundled CBC solver)
- HTMX (dashboard interactivity)

Build sequence:
1. Ingest — FPL API → SQLite, weekly snapshots (players, teams, fixtures, squad, mini-league)
2. Projection model — xP per player per GW: start probability, attack (xG/goals blend), defence, defensive-contribution; minutes-based regression; fixture and home/away adjustment
3. Optimizer — PuLP: best XI, captain (ceiling-weighted, fixture-adjusted), transfer verdict (roll / free / −4 hit over horizon), best squad from scratch
4. Decision layer — chip advisor, price watch, deadline awareness, replacement priority
5. Rival layer — differentials, threats, elite ownership, chip availability
6. Dashboard — server-rendered + HTMX; multi-user team selection; live stacked what-if
7. Deploy — Render, with a startup refresh to populate the ephemeral database

## Known Constraints

- Free-tier hosting spins down when idle — first visit after a pause has a cold-start delay while the database repopulates.
- Server runs on `now_cost` for selling prices (the exact-price endpoint needs an authenticated session that expires hourly); the difference never exceeds ~£0.2m and doesn't change decisions.
- History-dependent features (variance-aware valuation, rotation modelling) need several gameweeks of accumulated snapshots and activate as that history builds.

## Iteration Notes

- Caught a gameweek-offset bug in the fixture logic — the projection was reading one gameweek ahead, which surfaced when a captain recommendation showed the wrong opponent. Fixed by validating output against real fixtures and aligning every fixture, opponent-form, and captaincy read to the correct week. Added a correctness-audit pass to verify tool output against ground truth rather than trusting plausible-looking results.
- Fixed an affordability bug where the transfer solver recommended a two-move combination that was only fundable as part of a three-move package; each transfer path is now solved independently and proven self-affordable.
- Added cumulative budget tracking so stacked what-if swaps reflect money already committed, never offering a player the manager can't afford given prior moves.
