# 02 — B2B Lead Qualification Pipeline

End-to-end Zapier automation: Apollo → OpenAI scoring → Airtable. Separate client-facing demo app built alongside for presentations.

**Status:** Production
**Stack:** Apollo · Zapier (Code by Zapier) · OpenAI API · Airtable
**Tags:** #lead-generation #ai-scoring #zapier #airtable #apollo

## What It Does

A production Zapier automation that pulls leads from Apollo, scores them via a direct OpenAI API call, and writes structured qualification records to Airtable. A separate white-themed demo app allows a user to input any company name and receive an instant AI qualification score — built specifically for non-technical decision maker presentations.

## System Components

| Component | Description |
|-----------|-------------|
| Lead source | Apollo.io export trigger |
| Scoring engine | Code by Zapier — direct OpenAI API call |
| Database | Airtable — deduplication via Apollo ID |
| Demo interface | Clean white-themed single-page app |

## Architecture

## Architecture
Apollo exports lead

→ Zapier trigger fires on new record

→ Code by Zapier calls OpenAI API directly

→ Scoring output: qualification tier + signal summary + recommended action

→ Airtable "Create or Update Record" using Apollo ID as deduplication key
Demo app (separate):

User inputs company name

→ Frontend calls scoring endpoint

→ Returns qualification result instantly

→ No backend dependency on production Zap

## Key Design Decisions

- Used Code by Zapier (direct API call) instead of AI by Zapier — more reliable, no authentication errors
- Apollo ID used as the stable deduplication key in Airtable — prevents duplicate records on re-exports
- Demo app built entirely separately from the production Zap — clients see a clean interface, not the pipeline
- Demo app hosted on GitHub Pages as a standalone static interface

## Rebuild-From-Prompt Protocol

**Accounts required:**
- Apollo.io
- Zapier (paid — Code by Zapier requires paid plan)
- OpenAI API
- Airtable

**Zap structure:**
1. Trigger: New row in Apollo export / Airtable table
2. Action: Code by Zapier — paste OpenAI scoring prompt with lead fields as variables
3. Action: Airtable — Create or Update Record, match on Apollo ID field

**Files in this folder:**
- `prompts/` — OpenAI scoring prompt used in Code by Zapier
- `schemas/` — Airtable field map, scoring output structure
- `notes/` — Code by Zapier vs AI by Zapier decision log

## Known Constraints

- Code by Zapier requires a paid Zapier plan
- OpenAI API costs apply per scoring call
- Demo app is presentational only — not connected to live production data

## Iteration Notes

- 2025-02 — Switched from AI by Zapier to Code by Zapier after repeated auth failures
- 2025-02 — Added deduplication logic after duplicate records appeared on re-export
- 2025-01 — Initial build
