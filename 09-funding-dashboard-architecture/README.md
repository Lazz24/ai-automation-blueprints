# 09 — Funding Action Items Dashboard Architecture

> Architecture for funding action capture with conditional routing and effort allocation.

**Status:** `Design`
**Stack:** ClickUp · Zapier · Airtable
**Tags:** `#crm-architecture` `#automation` `#clickup`

---

## What It Does

An architecture design for a funding action capture system. Effort allocation is driven by conditional routing: high-value opportunities are escalated automatically, low-signal ones are deprioritised without manual review. ClickUp handles task creation, ownership, and deadlines.

---

## System Components

| Component | Description |
|-----------|-------------|
| Action capture | System for logging and categorising funding actions |
| Effort allocation | Routing logic assigning effort based on opportunity signals |
| Conditional routing | High-value → escalate / low-signal → deprioritise |
| ClickUp integration | Task creation with owner and deadline assignment |

---

## Architecture

    Funding action identified
    → Captured in Airtable
    → Conditional routing evaluates signals
    → High-value: ClickUp task created + escalated
    → Low-signal: marked low-priority, no task created
    → Effort allocation updated in dashboard view

---

## Rebuild-From-Prompt Protocol

**Accounts required:**
- [ ] ClickUp
- [ ] Zapier
- [ ] Airtable

**Files in this folder:**
- `schemas/` — ClickUp task integration, conditional routing logic
- `notes/` — effort allocation model

---

## Known Constraints

- Design phase only — not yet built in production
- Routing thresholds need calibration against real funding data

---

## Iteration Notes

- `2025-01` — Architecture design completed
