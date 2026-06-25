# 06 — n8n Operational Automation Systems

> POS data ingestion, staffing intelligence, and decision routing for a restaurant operation.

**Status:** `Active`
**Stack:** n8n · JSON · POS data · Webhook triggers
**Tags:** `#automation` `#data-pipeline` `#n8n`

---

## What It Does

A set of n8n workflows handling POS data ingestion, transformation, staffing triggers, and decision routing. Includes a staffing scheduler that surfaces actionable recommendations from raw transaction data. Workflow backup strategy documented for rebuild after n8n trial expiry.

---

## System Components

| Component | Description |
|-----------|-------------|
| POS ingestion | Pipeline pulling raw transaction data into n8n |
| Transformation | Data normalisation and structuring logic |
| Staffing triggers | Volume threshold logic surfacing staffing recommendations |
| Decision routing | Routing automation based on transformed data |
| JSON export | Structured output format for downstream consumption |
| Backup strategy | Workflow export and documentation protocol |

---

## Architecture

    POS system exports data
    → n8n webhook receives payload
    → Transformation node normalises structure
    → Volume thresholds evaluated
    → Staffing recommendation generated
    → Decision routing fires relevant action
    → JSON export written to output destination

---

## Key Design Decisions

- n8n chosen over Zapier — better JSON handling and free self-host option
- Staffing logic is threshold-based — no AI layer needed at current volume
- Workflow backup exported after every significant change
- JSON export structure documented so workflows can be reconstructed after trial expiry

---

## Rebuild-From-Prompt Protocol

**Accounts required:**
- [ ] n8n (self-hosted or cloud)
- [ ] POS system with export/webhook capability

**Files in this folder:**
- `workflows/` — POS ingestion pipeline, staffing scheduler logic
- `schemas/` — JSON export structure
- `notes/` — workflow backup strategy

---

## Known Constraints

- POS integration specific to one system — different providers need different ingestion logic
- Self-hosted n8n requires server maintenance

---

## Iteration Notes

- `2025-01` — Backup and export strategy added after trial expiry concern
- `2025-01` — Initial build
