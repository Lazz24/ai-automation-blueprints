# 05 — TechPort Radar & Signal Scoring Engine

> Four-dimensional lead scoring in Airtable. Automation triggers enrichment when score thresholds are crossed.

**Status:** `Active`
**Stack:** Airtable · Zapier · Apollo · OpenAI API
**Tags:** `#ai-scoring` `#crm-architecture` `#airtable` `#apollo`

---

## What It Does

A multi-layer scoring system built in Airtable. Leads are evaluated across four dimensions: AI Score, Intent Score, Signals, and Friction. Formula architecture handles the scoring logic; automation handles enrichment triggers when thresholds are crossed. Feeds directly into outreach sequencing.

---

## System Components

| Component | Description |
|-----------|-------------|
| AI Score | LLM-generated fit score based on company profile |
| Intent Score | Behavioural and signal-based intent rating |
| Signals | Observed positive indicators (hiring, funding, tech signals) |
| Friction | Observed blockers or risk factors |
| Formula architecture | Airtable formulas combining dimensions into composite score |
| Enrichment trigger | Automation fires on threshold breach |
| Rebuild prompt | Full reconstruction prompt for recreating the system |

---

## Architecture

    Lead enters Airtable from Apollo
    → AI Score generated via automation
    → Intent Score + Signals + Friction populated manually or via enrichment
    → Airtable formula calculates composite score
    → Score crosses threshold → enrichment trigger fires
    → Lead moves into prioritised outreach queue

---

## Key Design Decisions

- Four dimensions kept separate — easier to debug and adjust individual weights
- Formula architecture documented separately so it can be rebuilt if Airtable base is reset
- Signal-to-opportunity mapping defines which signals carry most weight per vertical
- Rebuild prompt created after original workflow was lost — now preserved in `prompts/`

---

## Rebuild-From-Prompt Protocol

**Accounts required:**
- [ ] Airtable
- [ ] Zapier
- [ ] Apollo

**Files in this folder:**
- `schemas/` — Airtable formula architecture, scoring field map
- `notes/` — signal-to-opportunity mapping, dimension weighting logic

---

## Known Constraints

- Intent Score requires manual input or third-party intent data source
- Formula complexity increases with each added dimension — keep changes documented

---

## Iteration Notes

- `2025-01` — Rebuild prompt created after workflow loss
- `2025-01` — Initial build
