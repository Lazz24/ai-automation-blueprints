# 01 — Funding Intelligence Engine

One-button grant analysis tool. Paste a grant description, get a structured fit assessment and effort estimate. Live on GitHub Pages.

**Status:** Live — lazz24.github.io/ai-automation-portfolio/01-funding-intelligence-engine
**Stack:** Groq · Airtable · GitHub Pages · Vanilla JS
**Tags:** #automation #ai-prompt-engineering #airtable #github-pages

## What It Does

A single-action web interface for grant analysis. The user pastes a grant description and clicks **Analyse**. The tool calls the Groq API (Llama 3.3 70B), returns a structured breakdown of fit, required actions, and effort estimate, and writes the result to Airtable for tracking.

Built for decision-making speed — the goal is a usable output in under 30 seconds, not a deep research report.

## System Components

| Component | Description |
|-----------|-------------|
| Frontend | Dark-themed single-page HTML/CSS/JS interface (`fie.html`) |
| LLM | Groq free-tier (Llama 3.3 70B) |
| Database | Airtable — auto-populated on each analysis |
| Hosting | GitHub Pages (static) |

## Architecture
User pastes grant description → clicks Analyse

→ Groq API called with structured prompt (Llama 3.3 70B)

→ Response returned to frontend and displayed

→ Result written to Airtable base (appmuzgkbZqGleLsv)

## Key Design Decisions

- Switched LLM provider from Anthropic → Gemini → Groq to stay on free tier throughout build
- Migrated hosting from Netlify → GitHub Pages to consolidate the portfolio under one static host
- Airtable field names in the base match the integration code exactly — do not rename fields without updating the call
- One-button UX was non-negotiable — clients and operators need zero learning curve

## Rebuild-From-Prompt Protocol

**Accounts required:**
- GitHub (free tier) — GitHub Pages enabled on the repo
- Groq (free tier) — groq.com
- Airtable (free tier) — base ID: `appmuzgkbZqGleLsv`

**Steps:**
1. Build single HTML file (`fie.html`) with form input and results display area
2. Wire the Groq call and Airtable write into the page logic
3. Commit to the repo with GitHub Pages enabled on the `main` branch
4. Confirm the page resolves at the Pages URL
5. Test with a real grant description

**Files in this folder:**
- `prompts/` — grant analysis system prompt
- `schemas/` — Airtable field map
- `notes/` — LLM provider switch log, hosting migration notes

## Known Constraints

- Groq free tier has rate limits — not suitable for high-volume usage
- Airtable write will fail silently if field names do not match exactly
- No authentication on the frontend — anyone with the URL can use it
- API credits refresh on a monthly cycle

## Iteration Notes

- 2025-03 — Switched from Gemini (gemini-2.0-flash quota error) to Groq llama-3.3-70b-versatile
- 2025-02 — Switched from Anthropic to Gemini due to API credit requirements
- 2025-02 — Initial build and Netlify deployment
- Migrated hosting from Netlify to GitHub Pages
