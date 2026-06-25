# 04 — Grant Monitor Bot

> Automated grant identification, tracking, and routing. Response-based effort allocation across 100+ weekly actions.

**Status:** `Active`
**Stack:** Zapier · ClickUp · OpenAI API · Email
**Tags:** `#automation` `#crm-architecture` `#clickup`

---

## What It Does

Automates the identification, tracking, and routing of grant opportunities. Handles a high-volume weekly cadence by prioritising effort based on response signals — not raw volume. Registration events trigger downstream workflows automatically. Includes a pricing and effort model embedded in the routing logic.

---

## System Components

| Component | Description |
|-----------|-------------|
| Grant identification | Workflow for sourcing and ingesting grant opportunities |
| Cadence model | 100+ action weekly structure with response-based prioritisation |
| Effort allocation | Deprioritises non-responders automatically |
| Escalation rules | Flags high-value or time-sensitive opportunities |
| ClickUp integration | Task creation, ownership assignment, handoff protocol |
| Pricing model | Effort estimate embedded in routing logic |

---

## Architecture

Grant opportunity identified

→ Ingested into tracking system

→ AI classifies fit and effort level

→ High-fit: escalated + ClickUp task created

→ Low-fit or no response: deprioritised automatically

→ Registration event → downstream workflow triggered

→ Handoff protocol initiated on acceptance

---

## Key Design Decisions

- Effort allocation is response-driven, not calendar-driven — system adapts to what is working
- ClickUp is the single source of truth for task ownership and deadlines
- Pricing model embedded so routing decisions reflect real cost, not just opportunity size

---

## Rebuild-From-Prompt Protocol

**Accounts required:**
- [ ] Zapier
- [ ] ClickUp
- [ ] OpenAI API

**Files in this folder:**
- `workflows/` — grant identification workflow, escalation rules
- `schemas/` — ClickUp integration logic
- `notes/` — cadence model, pricing and effort model

---

## Known Constraints

- Grant source data quality varies — some sources require manual review before ingestion
- ClickUp task structure must be maintained consistently for handoff protocol to work

---

## Iteration Notes

- `2025-01` — Initial build
