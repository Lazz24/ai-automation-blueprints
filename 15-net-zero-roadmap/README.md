# 15 — Net Zero Roadmap Generator

> AI-powered tool that turns a simple company profile into a structured Net Zero roadmap — executive summary, milestone timeline, quick wins, risks, and a downloadable branded PDF.

**Status:** `Live` — [lazz24.github.io/ai-automation-portfolio/15-net-zero-roadmap](https://lazz24.github.io/ai-automation-portfolio/15-net-zero-roadmap/)
**Stack:** Groq · Cloudflare Workers · Supabase · jsPDF · GitHub Pages
**Tags:** `#ai-prompt-engineering` `#cloudflare-workers` `#supabase` `#esg` `#pdf-generation`

---

## What It Does

A user fills in a short company profile, and the tool generates a complete Net Zero roadmap: an executive summary, a milestone timeline, quick wins, and key risks — then renders it to a downloadable branded PDF. The Groq API key is never exposed to the browser; all AI calls route through a Cloudflare Worker acting as a secure proxy and secret vault.

---

## System Components

| Component | Description |
|-----------|-------------|
| Frontend | HTML/JS form on GitHub Pages — 4-field company profile intake |
| API proxy | Cloudflare Worker — key vault, CORS handling, request routing |
| AI | Groq (llama-3.3-70b-versatile) — structured roadmap generation |
| Database | Supabase (Postgres) — roadmap record storage |
| PDF | jsPDF — client-side branded PDF, no server dependency |

---

## Architecture

    User fills 4 fields: sector, company size, baseline emissions, target year
    → POST sent to Cloudflare Worker
    → Worker calls Groq with structured prompt (key stays server-side)
    → Groq returns JSON roadmap
    → Worker writes record to Supabase
    → Frontend renders roadmap + offers PDF download (jsPDF)

---

## Key Design Decisions

- Cloudflare Worker used as API proxy — keeps the Groq key server-side, never exposed in the browser (the secure pattern, not a client-side call)
- jsPDF chosen for PDF generation — renders entirely client-side, no server or third-party PDF service needed
- Supabase for storage — free-tier Postgres with minimal setup
- 4-field intake kept deliberately short — one-button-style UX, low friction to a usable output

---

## Database Schema

Supabase table: `roadmaps`

Fields: `id` · `created_at` · `sector` · `company_size` · `baseline_emissions` · `target_year` · `summary` · `milestones` (jsonb) · `quick_wins` (jsonb) · `risks` (jsonb)

---

## Rebuild-From-Prompt Protocol

**Accounts required:**
- [ ] GitHub (Pages enabled)
- [ ] Cloudflare (Workers)
- [ ] Groq (free tier) — groq.com
- [ ] Supabase (free tier)

**Steps:**
1. Deploy the intake form (`index.html`) to GitHub Pages
2. Create a Cloudflare Worker as the API proxy — store the Groq key as a Worker secret
3. Worker receives the POST, calls Groq with the structured roadmap prompt, returns JSON
4. Create the Supabase `roadmaps` table and write the record from the Worker
5. Render the roadmap on the frontend and wire the jsPDF download
6. Test the full path with a real company profile

**Files in this folder:**
- `worker.js` — Cloudflare Worker proxy logic
- `schemas/` — Supabase table schema
- `notes/` — build learnings, testing gotchas

---

## Known Constraints

- Groq free tier subject to rate limits
- Supabase enables Row Level Security by default — anon writes require disabling RLS or adding an explicit policy
- Cloudflare Workers reset headers/body on navigation — test with PowerShell or Postman, not the in-browser tester
- Cloudflare's HTTP tester defaults to GET, not POST — a silent cause of failed test calls during build

---

## Iteration Notes

- `2026-03` — Initial build and live deployment; full pipeline operational (Worker proxy, Groq scoring, Supabase write, jsPDF export)
