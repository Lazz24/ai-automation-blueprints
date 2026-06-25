# 13 — CSRD Compass — Sustainability Readiness Tool

> Free AI-powered CSRD readiness assessment for Dutch SMEs. 15-question intake, scored across three ESRS pillars, delivered by email.

**Status:** `Live` — [lazz24.github.io/ai-automation-portfolio/13-csrd-compass](https://lazz24.github.io/ai-automation-portfolio/13-csrd-compass/)
**Stack:** Make.com · Groq · Airtable · Notion · Gmail · HTML/JS
**Tags:** `#ai-scoring` `#make-automation` `#groq` `#airtable` `#notion` `#esrs` `#b2b-tool`

---

## What This Is

A free, AI-powered CSRD readiness assessment tool targeting Dutch SMEs facing EU Corporate Sustainability Reporting Directive compliance deadlines. Users complete a 15-question intake form and receive a personalised readiness report delivered by email — scored across three ESRS pillars: Environment, Social, and Governance.

Built as a top-of-funnel lead generation tool. The free assessment is the entry point; the upsell is a paid CSRD Action Tracker engagement.

---

## System Components

| Layer | Tool | Role |
|---|---|---|
| Frontend | HTML/CSS/JS | 15-question intake form, confirmation screen |
| Hosting | GitHub Pages | Free static hosting |
| Automation | Make.com | Webhook receiver, pipeline orchestrator |
| AI | Groq (llama-3.3-70b-versatile) | CSRD scoring, gap analysis, action plan generation |
| Database | Airtable | Submission records |
| CRM | Notion | Assessment records with scores |
| Email | Gmail via Make | Branded HTML report delivery |

---

## Architecture Overview

    User Form (GitHub Pages)
        → Make Webhook [1]
        → HTTP/Groq AI [2]
        → Tools Set Variable [6]
        → JSON Parse [8]
        → Notion Create Record [4]
        → Airtable Create Record [5]
        → Gmail Send Report [9]

---

## Scoring Logic

**15 questions across 4 pillars:**

- **Pillar 0 — Scope & Obligation** (qualifier, sets urgency tier, unscored)
- **Pillar 1 — Environment** (40% weight): energy tracking, emissions, targets
- **Pillar 2 — Social** (35% weight): HR policy, diversity, supplier conduct, safety
- **Pillar 3 — Governance** (25% weight): ESG leadership, ethics, reporting, risk

**Point values:** Formal/Yes = 3pts · Informal/Partial = 1.5pts · No/Don't know = 0pts

**Readiness tiers:** CSRD-Ready (80–100%) · Developing (55–79%) · Early Stage (30–54%) · Not Started (0–29%)

**Urgency flags:** HIGH / MEDIUM / LOW based on obligation tier + score combination

---

## AI Output Structure

Groq returns a strict JSON object containing:

- `company_profile` — obligation tier, urgency, reporting deadline
- `scores` — environment, social, governance, overall (weighted percentage + tier)
- `pillar_summaries` — one paragraph per pillar
- `top_gaps` — gap description, why it matters, ESRS reference
- `action_plan` — prioritised actions with effort and impact ratings
- `executive_summary` — full narrative summary

---

## Key Design Decisions

- **Email delivery over in-browser results:** Make webhooks return "Accepted" only — results cannot be returned synchronously to the browser. Email is the intentional delivery mechanism, not a workaround.
- **no-cors removed:** Initial CORS fix broke JSON parsing by wrapping payload in a `value` key. Removed in favour of direct POST — Make webhooks accept cross-origin requests natively.
- **Notion/Airtable fields as Text not Select:** AI output phrasing is inconsistent; Text fields avoid validation errors on select options.
- **Groq free tier:** Selected over Anthropic/OpenAI due to zero API credit requirement during build and demo phase.

---

## Rebuild-From-Prompt Protocol

To reconstruct this system from scratch:

1. Deploy `index.html` to any static host (GitHub Pages, Netlify, etc.)
2. Create a Make scenario with modules: Webhook → HTTP (Groq) → Tools → JSON Parse → Notion → Airtable → Gmail
3. Use the system prompt in `/prompts/groq-system-prompt.md` for the Groq API call
4. Map all 15 question fields from the webhook payload to the HTTP module body
5. Configure Notion and Airtable field mappings as documented in `/schemas/`
6. Set Notion/Airtable select fields to Text type to avoid AI phrasing mismatch errors

---

## Known Constraints

- Results screen shows confirmation only — no live scores in browser (by design)
- Groq free tier subject to rate limits; upgrade path is paid Groq or switch to OpenAI
- Make free tier: ~1000 ops/month, resets monthly
- GitHub Pages requires public repo on free GitHub plan

---

## Iteration Notes

- **v1.0** — Full pipeline operational: Groq scoring, Notion, Airtable, Gmail delivery
- **Potential v2.0** — Return path via polling or serverless function to show live results in browser
- **Potential v3.0** — CSRD Action Tracker upsell flow triggered post-assessment
