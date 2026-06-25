# 03 — Apollo → AI → Executive Intelligence Engine

Full-path system from lead ingestion to routed intelligence. Handles signal extraction, executive summary generation, and channel routing.

**Status:** Active
**Stack:** Apollo · OpenAI API · Zapier · Airtable · Slack
**Tags:** #lead-intelligence #ai-scoring #ai-prompt-engineering #zapier #airtable #slack

## What It Does

Takes raw prospect data from Apollo and LinkedIn, runs it through a structured AI prompt pipeline to extract company signals and generate an executive summary, then routes the output to the appropriate channel (email or Slack) based on score threshold. Covers the full path from lead ingestion to actionable intelligence with messaging tone control and follow-up constraint logic built in.

## System Components

| Component | Description |
|-----------|-------------|
| Ingestion | Apollo lead export + LinkedIn signal input |
| AI processing | OpenAI — signal extraction, exec summary generation |
| Positioning layer | Painkiller + bonus value framing in prompt |
| Routing | Score threshold → email or Slack |
| Follow-up constraint | 2-week lock to prevent noise |
| Tone control | Messaging style embedded in system prompt |
| Rebuild kit | Copilot rebuild prompts for full reconstruction |

## Architecture

Apollo export → lead record created

→ LinkedIn signal data appended

→ AI prompt: extract signals + friction hypothesis

→ AI prompt: generate executive summary

→ AI prompt: draft outreach with tone control

→ Score evaluated against threshold

→ Route: high score → Slack alert / standard → email

→ Follow-up flag set — 2-week constraint applied

## Key Design Decisions

- Painkiller and bonus value framing embedded in prompt — ensures consistent positioning
- 2-week follow-up constraint enforced in Zap logic, not left to the operator
- Messaging tone control is a prompt parameter — adjustable per campaign without rebuilding the Zap
- Routing split (Slack vs email) is threshold-based — high-signal leads get immediate visibility
- Internal routing only — AI output comes to operator, not directly to prospects

## Rebuild-From-Prompt Protocol

**Accounts required:**
- Apollo.io
- Zapier
- OpenAI API
- Airtable
- Slack

**Steps:**
1. Set up Apollo export trigger in Zapier
2. Load signal extraction prompt (see `prompts/signal-extraction-prompt.md`)
3. Load executive summary prompt (see `prompts/exec-summary-prompt.md`)
4. Load positioning prompt (see `prompts/painkiller-positioning-prompt.md`)
5. Configure routing logic — set score threshold for Slack vs email
6. Set 2-week follow-up date field in Airtable
7. Test with 3 leads before going live

**Files in this folder:**
- `prompts/` — signal extraction, exec summary, positioning, tone control, copilot rebuild prompts
- `schemas/` — Apollo field map, Airtable schema
- `notes/` — routing logic, follow-up constraint design, internal notification pattern

## Known Constraints

- LinkedIn signal data requires manual input or separate enrichment step
- Tone control works within a defined range — niche industries may need prompt adjustment
- 2-week constraint is date-based — if Airtable record is deleted, constraint resets

## Iteration Notes

- 2025-02 — Added tone control parameter to prompt
- 2025-01 — Added 2-week follow-up constraint after operator feedback on noise
- 2025-01 — Initial build
