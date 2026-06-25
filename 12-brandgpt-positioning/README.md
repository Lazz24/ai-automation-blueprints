# 12 — BrandGPT / Personal Positioning System

> AI-assisted personal brand and positioning framework. Clarifies lane, narrative, and service packaging for faster opportunity conversion.

**Status:** `Active`
**Stack:** AI Prompt Engineering · Brand Strategy · LinkedIn
**Tags:** `#gtm` `#ai-prompt-engineering` `#outreach-system`

---

## What It Does

A positioning framework built with AI assistance to clarify personal brand direction, define the right market lane, and package services in a way that opens opportunities faster. Goes beyond content marketing — the focus is on narrative clarity, service positioning, and making the right prospects immediately understand what value is on offer.

Covers Dutch-market positioning, AI/automation consultant framing, and the connection between personal brand and outreach effectiveness.

---

## System Components

| Component | Description |
|-----------|-------------|
| Lane definition | Clarity on which market, buyer, and problem to focus on |
| Brand narrative | Positioning language connecting background to current offer |
| Service packaging | How to frame and present automation/GTM services |
| 25-step plan | Structured plan for brand-to-revenue execution |
| Image generation prompts | Visual identity prompts for LinkedIn and portfolio assets |
| Profile/portfolio framing | How positioning connects to GitHub, LinkedIn, and outreach |

---

## Architecture

    Positioning audit → lane definition
    → Narrative constructed around real strengths
    → Service packages framed for target buyer
    → LinkedIn profile and outreach copy aligned to narrative
    → Portfolio assets (GitHub, PDF snapshot) reflect positioning
    → Outreach messaging pulls from brand narrative

---

## Key Design Decisions

- Positioning built around real delivered work — not aspirational claims
- Dutch-market context shaped the framing — direct, outcome-focused, no fluff
- Brand narrative feeds directly into outreach messaging tone and structure
- Portfolio and LinkedIn treated as one connected system, not separate assets

---

## Rebuild-From-Prompt Protocol

**No platform accounts required — this is a strategy and prompt asset.**

**Steps:**
1. Run positioning audit prompt (see `prompts/positioning-audit-prompt.md`)
2. Define lane using lane-definition framework (see `notes/lane-definition.md`)
3. Draft service packaging language
4. Align LinkedIn headline, About section, and Featured content to narrative
5. Pull tone control rules into outreach messaging prompts

**Files in this folder:**
- `prompts/` — positioning audit prompt, image generation prompts, brand narrative prompts
- `notes/` — lane definition, 25-step plan, Dutch-market framing, service packaging logic

---

## Known Constraints

- Brand positioning is a living document — needs updating as services and focus evolve
- Effectiveness depends on consistent application across all outreach and profile touchpoints

---

## Iteration Notes

- `2026-03` — Added to portfolio as standalone project
- `2025-01` — Framework developed and actively used
