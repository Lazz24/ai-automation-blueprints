# 22 — ETL Data Pipeline

> A Bronze → Silver → Gold medallion data pipeline: ingests raw CSV or API data, validates and enriches it, aggregates it, and produces an AI-narrated branded PDF report. Built and proven locally; deploying to the Magic Hat product tree.

**Status:** `Built — deploying` — proven locally, scheduled for deployment to `etl.magichat.consulting` as part of a unified product-tree launch
**Stack:** Python · FastAPI · PostgreSQL · pandas · APScheduler · fpdf2 · Groq
**Tags:** `#etl` `#data-pipeline` `#medallion-architecture` `#fastapi` `#pandas` `#data-engineering`

---

## What It Does

A medallion-architecture ETL pipeline. Raw data enters as Bronze, is validated and cleaned into Silver, and is aggregated into Gold metrics. The Gold layer is then narrated by an LLM into a plain-language report and exported as a branded PDF. The system runs on demand or on a schedule, and exposes a full client-facing dashboard.

It is the data-ingestion layer of the Magic Hat product tree — it can push its output reports directly into the RAG Engine, building a compounding time-series knowledge base over time.

---

## System Components

| Component | Description |
|-----------|-------------|
| Bronze | Adaptive-column ingestion of CSV and API data |
| Silver | Two-gate validation and anomaly detection, modular enrichment |
| Enrichment | Isolated provider module — enrichment logic kept separate from validation |
| Gold | pandas aggregation into final metrics |
| Narration | Question-aware LLM narration of the Gold metrics (Groq) |
| Reporting | Branded PDF export (fpdf2) |
| Scheduler | APScheduler — automated runs |
| API | FastAPI service — upload, analytics, dataset history, config CRUD, push-to-RAG |
| Dashboard | Client-facing UI — drag-and-drop upload, Bronze→Silver→Gold→AI progress, Gold table, PDF download, admin panel |

---

## Architecture

    Raw data (CSV upload or API pull)
    → BRONZE: adaptive-column ingestion, stored raw
    → SILVER: validation gates + anomaly detection + enrichment
    → GOLD: pandas aggregation into final metrics
    → AI narration: LLM describes the Gold metrics (narrates, never computes)
    → PDF report generated (branded)
    → optional: push report to the RAG Engine (Bridge A)

---

## Key Design Decisions

- Medallion architecture (Bronze/Silver/Gold) — clean separation between raw, validated, and aggregated data; each layer is independently inspectable
- Anti-hallucination discipline — the LLM narrates already-computed numbers; it never performs the arithmetic. Aggregation is pandas' job, narration is the LLM's job.
- Enrichment isolated in its own module — provider logic is swappable without touching validation
- Two-gate validation in Silver — structural and anomaly checks before data is allowed into Gold
- Standalone-first — fully functional on its own; the RAG bridge is an addition, not a dependency
- Each push to the RAG Engine compounds — over weeks the corpus becomes a time-series record, not a snapshot

---

## Validation

- Passed evaluation harness (10/10)
- Bridge A verified end-to-end — pushed report answered against in the RAG Engine with exact Gold metrics grounded to the source page

---

## Rebuild-From-Prompt Protocol

**Stack required:**
- [ ] Python 3.12 + FastAPI
- [ ] PostgreSQL
- [ ] pandas, httpx, APScheduler, fpdf2
- [ ] Groq API (narration)

**Build sequence:**
1. Database init — tables and views for all three layers
2. Bronze — adaptive ingestion (CSV + API)
3. Silver — validation gates, anomaly detection, enrichment module
4. Gold — pandas aggregation
5. Narration — question-aware LLM step over Gold metrics
6. Reporting — branded PDF export
7. Entry point + scheduler for automated runs
8. API endpoints + client dashboard
9. Eval-gate before shipping

---

## Known Constraints

- Currently runs locally — public deployment pending the product-tree droplet launch
- Narration quality bounded by the LLM; the numbers themselves are deterministic (pandas)
- Groq free tier is rate-limited
- Ingestion adapts to columns but very irregular sources may need a custom Bronze step

---

## Iteration Notes

- Built sequentially and eval-gated locally (10/10)
- Bridge A (ETL → RAG) built and verified end-to-end
- Dashboard themed to match the RAG Engine visual identity (DM Sans + DM Mono, navy/orange)
- Planned — deployment to `etl.magichat.consulting` in the unified tree launch
