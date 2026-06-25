# 17 — Execution Radar (Client Build)

> A multi-table client execution radar that tracks projects, tasks, stakeholders, and decisions in one operational view — with automated weekly snapshots, overdue tracking, and AI-generated meeting briefs.

**Status:** `Active` — private client engagement (case study)
**Stack:** Airtable · Zapier · ClickUp · Slack · Groq
**Tags:** `#crm-architecture` `#automation` `#airtable` `#zapier` `#client-build`

---

## What It Does

A client-facing execution radar built for a consulting engagement. It consolidates an organisation's active projects, tasks, stakeholders, and key decisions into a single operational system, then layers automation and AI on top to surface what needs attention each week — which projects are slipping, which tasks are overdue, where decisions are stalling, and where leadership attention is thin.

The goal is to replace scattered status-chasing with one живой view the principal can act on directly.

---

## System Components

| Component | Description |
|-----------|-------------|
| Data model | Multi-table base: Projects, Tasks, Stakeholders, Weekly Snapshots, plus an intelligence layer (Assumptions, Decision Points, Dependencies) |
| Operational views | Filtered views for critical/warning items, stale and overdue tasks, stakeholder overload, and snapshot trends |
| Interface layer | Published interface pages — one operational, one strategic |
| Snapshot automation | Scheduled weekly snapshot capture for time-series tracking |
| Sync automations | Two-way task/project sync with an external project-management tool |
| Digest + brief automations | Automated weekly digest and pre-meeting brief delivered to chat + email |
| AI intelligence layer | AI-assisted weekly change summaries and pattern detection — synthesis only; the principal makes the judgments |

---

## Architecture

    Source data entered / synced into multi-table base
    → Scheduled automations capture weekly snapshots
    → Overdue and staleness counters updated automatically
    → Operational + strategic interface pages render the live view
    → Weekly digest and pre-meeting brief generated and delivered (chat + email)
    → AI layer summarises what changed and flags where judgment is needed
    → Principal reviews and acts — AI accelerates synthesis, human decides

---

## Key Design Decisions

- Separation of operational and strategic layers — day-to-day execution and higher-level decision tracking live on different interface pages
- Weekly snapshots stored as a time-series — enables trend analysis (which areas repeatedly slip) rather than just current-state
- AI is used for synthesis, never for decisions — it summarises and surfaces; the human makes every call
- Two-way sync with an external PM tool — the radar reflects reality without forcing the client to abandon their existing workflow
- Built in phases — operational base first, then automation, then the intelligence layer, each shipped and verified before the next

---

## Rebuild-From-Prompt Protocol

**Accounts required:**
- [ ] Airtable
- [ ] Zapier (paid — multi-step automations)
- [ ] Project-management tool with API (for sync)
- [ ] Chat + email delivery (e.g. Slack + Gmail)
- [ ] Groq (AI summary layer)

**Build sequence:**
1. Build the multi-table base (projects, tasks, stakeholders, snapshots)
2. Add operational views (critical/warning, overdue, stale, overload, trend)
3. Build snapshot and overdue-counter automations
4. Publish operational + strategic interface pages
5. Add two-way sync with the external PM tool
6. Add weekly digest and pre-meeting brief automations
7. Layer in the intelligence tables (assumptions, decisions, dependencies) and the AI summary step

---

## Known Constraints

- Time-series value compounds over time — early weeks have limited trend data
- Sync reliability depends on the external PM tool's API behaviour
- AI summary quality depends on data being kept current in the base
- Client-specific configuration — a rebuild for a different organisation needs its own data model tuning

---

## Iteration Notes

- Phase 1 — Operational base, views, and first automations
- Phase 2 — Interface pages, two-way PM sync, digest + pre-meeting brief
- Phase 3 — Intelligence layer: trend analysis, AI meeting-prep, engagement indicators, weekly change summaries
