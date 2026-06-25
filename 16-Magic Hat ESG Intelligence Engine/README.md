# 16 — Magic Hat ESG Intelligence Engine

> AI-powered ESG reporting automation. Reads impact metrics from Google Sheets, runs a multi-pass Groq analysis, and delivers a branded HTML report by email — with no manual intervention.

**Status:** `In Development` — core pipeline complete; Make.com rebuild pending for production stability
**Stack:** ActivePieces · Google Sheets · Groq · QuickChart · Resend
**Tags:** `#automation` `#ai-prompt-engineering` `#esg` `#groq` `#reporting`

---

## Overview

An AI-powered ESG reporting automation that reads impact metrics from Google Sheets, runs them through a multi-pass Groq analysis pipeline, and delivers a branded HTML report to stakeholders via email — with zero manual intervention required.

Built to demonstrate multi-agent AI orchestration, dynamic data pipelines, and client-facing automation design.

---

## Stack

| Tool | Role |
|---|---|
| ActivePieces | Workflow automation (webhook, scheduling, routing) |
| Google Sheets | Data source — metrics, config, audit log |
| Groq (llama-3.3-70b-versatile) | AI engine — anomaly detection + narrative generation |
| QuickChart | Auto-generated chart visuals (gauge, bar, line) |
| Resend | Branded HTML email delivery |

---

## Architecture

    TRIGGER LAYER
    ├── Webhook (manual / demo)
    ├── Schedule (cron)
    └── Sheet Watch (new data)
             │
             ▼
    DATA LAYER
    └── Google Sheets Read
        ├── config tab (company name, recipients, target score)
        └── metrics tab (current + previous period)
             │
             ▼
    AI LAYER — PASS 1 (Anomaly Detection)
    └── Groq: Scans all metrics
        ├── Flags anomalies (spikes, drops, missed targets)
        ├── Scores each category (0–100)
        └── Returns structured JSON
             │
             ▼
    AI LAYER — PASS 2 (Narrative Generation)
    └── Groq: Receives Pass 1 JSON + raw metrics
        ├── Executive summary
        ├── Highlights and concerns
        ├── Anomaly callouts with severity
        ├── Period-on-period commentary
        └── Tone-switched: internal (direct) vs external (polished)
             │
             ▼
    CHART LAYER
    └── QuickChart API (free, no auth)
        ├── Doughnut gauge — overall ESG score
        ├── Bar chart — category scores vs target
        └── Line chart — trend over time
             │
             ▼
    DELIVERY LAYER
    └── Resend — branded HTML email
        ├── Magic Hat header + score band
        ├── Embedded chart visuals
        ├── AI-generated narrative
        ├── View Full Dashboard button
        └── Request Deeper Analysis button (webhook)
             │
             ▼
    AUDIT LAYER
    └── Google Sheets write-back
        ├── Run ID, timestamp, trigger type
        ├── ESG score, anomalies flagged
        └── Drill-down requested (yes/no)

---

## Google Sheet Schema

### `config` tab
| Column | Description |
|---|---|
| company_name | Client name injected into email and dashboard |
| logo_url | Optional logo URL for email header |
| report_title | Email subject and dashboard heading |
| internal_recipients | Comma-separated internal email addresses |
| external_recipients | Comma-separated external email addresses |
| reporting_currency | Currency for financial metrics |
| target_score | ESG benchmark score (default: 75) |
| timezone | Cron scheduling timezone |
| drill_down_webhook_url | Webhook URL for deeper analysis requests |

### `metrics` tab
One row per reporting period. Columns include:
`period_label`, `period_date`, `is_current`, `scope_1_emissions_tco2`, `scope_2_emissions_tco2`, `scope_3_emissions_tco2`, `energy_consumption_mwh`, `renewable_energy_pct`, `water_usage_m3`, `waste_recycled_pct`, `employee_headcount`, `gender_diversity_pct`, `safety_incidents`, `training_hours_per_employee`, `community_investment_eur`, `board_independence_pct`, `whistleblower_reports`

### `audit_log` tab
Auto-populated by the workflow after every run.

---

## Groq Prompt Design

### Pass 1 — Anomaly Detection
- Input: raw metrics JSON + target score
- Output: structured JSON with `overall_score`, `anomalies[]`, `category_scores{}`
- Temperature: 0.3 (precise, deterministic)

### Pass 2 — Narrative Generation
- Input: Pass 1 JSON + raw metrics + recipient type
- Output: HTML narrative with Executive Summary, Highlights, Concerns, Anomaly Alerts, Period Comparison, Recommendation
- Temperature: 0.5 (professional, readable)
- Tone switching: internal = analytical, external = stakeholder-friendly

---

## Client-Facing Experience

From the client's perspective this is entirely effortless:

1. Fill in the Google Sheet with metrics
2. Report arrives in their inbox on schedule
3. Email shows score, highlights, and anomaly alerts at a glance
4. One click opens the full interactive dashboard
5. One click requests a deeper analysis — follow-up email arrives automatically

Clients never touch the workflow, the AI, or any technical tool.

---

## Current Status

| Component | Status |
|---|---|
| Google Sheet schema | Complete |
| ActivePieces workflow | Built |
| Groq Pass 1 (anomaly detection) | Working |
| Groq Pass 2 (narrative generation) | Working |
| QuickChart integration | Working |
| Resend email delivery | Working |
| Audit write-back | Working |
| Variable resolution in email | In progress |
| Make.com rebuild | Planned |
| Groq Pass 3 (drill-down) | Planned |
| Schedule + sheet-watch triggers | Planned |

---

## Known Issues

- ActivePieces Code step inputs have inconsistent variable resolution behaviour — score and narrative values sometimes fail to pass through to the email template. Root cause: AP code step input binding. Fix: rebuild in Make.com where variable passing is reliable.
- Gmail blocks QuickChart external images by default — users need to enable external image loading in Gmail settings.

---

## Rebuild Plan (Make.com)

The full pipeline logic is complete and proven. The Make.com rebuild will:
- Replace ActivePieces with Make.com modules
- Use Make's native HTTP module for Groq calls
- Use a Set Variable module to extract Groq content strings before parsing
- Add the drill-down flow as a separate Make scenario
- Add the schedule and sheet-watch triggers

---

## Why This Project

This build demonstrates:
- Multi-pass AI reasoning (detect → narrate → drill-down)
- Dynamic data pipelines from structured spreadsheet input
- Client-facing automation with zero technical friction
- ESG/sustainability domain expertise
- Production-grade email delivery with branded templates

---

## Iteration Notes

- Core pipeline operational: Groq Pass 1 + Pass 2, QuickChart, Resend delivery, audit write-back
- Make.com rebuild planned to resolve ActivePieces variable-resolution issue and add scheduling + drill-down
