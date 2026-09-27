# 29 — Sell the Couch

> A four-agent workflow that takes a used item from raw photos to a confirmed pickup: it writes the listing, posts it, triages buyer messages, and schedules the handoff — with a human approval gate at every step that sends or posts. Built and run live to sell two real items in Budapest.

**Status:** `Built — run live` — four agents complete; used to list a couch and armchair on Jófogás and Facebook Marketplace, both live and priced in HUF
**Stack:** Claude Code (Sonnet 5) · Claude in Chrome · Gmail connector · JSON state store
**Tags:** `#agents` `#multi-agent` `#automation` `#claude-code` `#browser-automation` `#human-in-the-loop`

---

## What It Does

Turns the chore of selling a used item into a guided pipeline. Give it photos and a few facts; it produces a priced, platform-tuned listing, posts it, handles the repetitive buyer messages, and books the pickup. Four single-purpose agents hand off to each other through one shared state file.

The design priority is a human gate on every irreversible action: the agents prepare, the human approves and sends. Nothing posts, replies, or shares an address without a person clicking the final button.

---

## System Components

| Agent | Description |
|-------|-------------|
| Listing Agent | Takes item facts + photos, generates the title and per-platform descriptions (Jófogás + Facebook, Hungarian), prices from comps or a set target, writes `listing_ready` |
| Publisher Agent | Drives a logged-in browser to fill the posting form from the listing; stops at the review screen for the human to post; writes `posted` |
| Responder Agent | Reads buyer emails, classifies each (still-available / lowball / logistics / serious), drafts approved replies, marks serious buyers — never sends |
| Scheduler Agent | Proposes pickup slots from set availability, confirms a time, releases the address only on confirmation, writes `pickup_confirmed` |
| State store | One `state.json` at the project root — the shared brain every agent reads and writes |

---

## Architecture

    Photos + facts → Listing Agent → state.json: listing_ready
    → Publisher Agent fills the form → [human posts] → state.json: posted
    → buyer emails arrive
    → Responder Agent classifies + drafts → marks buyer serious
    → Scheduler Agent proposes times → [human sends]
    → buyer picks a slot → address released → state.json: pickup_confirmed

---

## Key Design Decisions

- Human gate on every side-effect — the agents are prepare-only; posting, sending, and address-release all wait for a person. The gate is a hard constraint, not a setting.
- One shared state file — the four agents coordinate through `state.json` alone; no agent calls another directly, so each can be run, tested, and fixed on its own.
- Two-level state — items carry their own status (`listing_ready → posted → pickup_confirmed`); buyers live in a per-item `buyers[]` array with their own status, so the message-handling agents have somewhere to write without clobbering the item.
- Address held until a real slot — the pickup address stays out of every message until a time is agreed, then appears only in the confirmation.
- Model tier matched to task — copy and triage run on Sonnet 5 Low; the browser step is flagged to bump higher only if the form-filling fumbles.
- Prepare-only agents don't chain automatically — each fires when the previous has produced something for it, which keeps the human in the loop between stages.

---

## Validation

- Run live: couch and armchair listed on both Jófogás and Facebook Marketplace, both priced correctly in HUF
- Listing Agent proven end to end — both items written to `state.json` with clean UTF-8 Hungarian copy and honest condition notes
- Publisher proven against a live Facebook form (listings went up); Jófogás posting confirmed
- Real-world corrections handled: currency defaulted to RON via account region, fixed to HUF; owner review caught listing inaccuracies, corrected across the live listing and the state store
- Responder and Scheduler written and wired to the shared buyer shape; proven once real buyer messages arrive

---

## Rebuild-From-Prompt Protocol

**Stack required:**
- [ ] Claude Code (skills run as `/agent-name` from the project root)
- [ ] Claude in Chrome (for the Publisher's browser step)
- [ ] Gmail connector (for the Responder's inbox access)
- [ ] A `state.json` at the project root

**Build sequence:**
1. Write the Listing Agent skill; run it per item to populate `state.json` with `listing_ready`
2. Write the Publisher Agent skill; confirm logged-in browser + a photo folder per item; run it to `posted`, human posts
3. Write the Responder Agent skill; agree the per-item `buyers[]` shape it writes into; wire Gmail
4. Write the Scheduler Agent skill; point it at the same `buyers[]` shape; set availability windows
5. Wrap: walk `state.json` through the full chain, confirm each agent reads what the last one wrote and the field names match across skills

---

## Known Constraints

- Not a deployed service — it is a set of Claude Code skills run locally, by design
- Marketplace posting and message-reading fight platform terms and anti-bot measures — the Publisher runs semi-supervised, not headless
- Responder assumes buyer messages arrive as full-text email — a "log in to read" stub would need a browser-based read path instead
- Availability windows are set values, not a live calendar — fine for a handful of buyers
- Built for one seller's own accounts and items, not multi-user

---

## Iteration Notes

- Built as a portfolio piece — a small, real, end-to-end multi-agent system, run against actual items rather than a demo
- Superseded an early single-agent idea; split into four single-purpose agents once the handoff chain became the point
- Encoding scare during the wrap: `state.json` read as garbled under default PowerShell `type`, confirmed clean UTF-8 with `type -Encoding UTF8` — file was fine, display was not
- Seam fix added in the wrap: Responder skips drafting for any item already `pickup_confirmed`, flags instead
- Planned — run the live buyer loop end to end (post → email → triage → pickup) to prove the two message-handling agents against real traffic
