# 08 — Hunter vs Harvest GTM Structuring

> GTM framework separating active hunting from passive nurture. Account prioritisation by signal strength.

**Status:** `Active`
**Stack:** Apollo · Airtable · Zapier · Sequencing logic
**Tags:** `#gtm` `#revenue-ops` `#outreach-system` `#apollo`

---

## What It Does

A GTM framework that segments accounts into Hunter (active outreach) and Harvest (passive nurture) tracks. Prioritises accounts by signal strength and friction score, sequences outreach by channel and timing, and structures GTM work into defined packages to prevent initiative overload.

---

## System Components

| Component | Description |
|-----------|-------------|
| Hunter track | Active outreach accounts — high signal, low friction |
| Harvest track | Nurture accounts — moderate signal, longer cycle |
| Account prioritisation | Signal + friction scoring determines track assignment |
| Outreach sequencing | Channel and timing rules per track |
| Work packages | GTM activity structured into defined units |
| Initiative overload diagnosis | Framework for identifying and cutting low-ROI activity |

---

## Architecture

    Account enters pipeline
    → Signal + friction score evaluated
    → Assigned to Hunter or Harvest track
    → Sequencing rules applied per track
    → Work packages structured and assigned
    → Initiative overload check run periodically

---

## Key Design Decisions

- Hard separation between Hunter and Harvest — avoids mixing urgency levels in the same sequence
- Work packages prevent scope creep — each unit has a defined start, end, and output
- Initiative overload diagnosis is a periodic review, not a continuous process

---

## Rebuild-From-Prompt Protocol

**Accounts required:**
- [ ] Apollo.io
- [ ] Airtable
- [ ] Zapier

**Files in this folder:**
- `schemas/` — GTM work packages structure, account prioritisation model
- `notes/` — outreach sequencing logic, initiative overload diagnosis framework

---

## Known Constraints

- Track assignment is only as good as the signal and friction scoring input
- Work packages require discipline to maintain — easy to let scope expand

---

## Iteration Notes

- `2025-01` — Initial build
