# 18 — Magic Hat TalentBridge

> Two-sided talent matching microsite. Students self-submit and get instant AI feedback; the client gets a scored inbound candidate pipeline with one-click outreach.

**Status:** `Demo` — front-end live on GitHub Pages with preloaded data; automation layer deferred
**Stack:** GitHub Pages · Make.com · Airtable · Anthropic API
**Tags:** `#two-sided-marketplace` `#ai-feedback` `#recruitment` `#airtable` `#make-automation`

---

## What It Does

A two-sided matching tool for student recruitment. On one side, students fill in a short profile (skill chips, free-text bio) and receive instant AI-generated feedback on submission — the immediate value return is what drives high completion rates. On the other, the client gets an inbound dashboard of scored candidates with AI summaries and a one-click generator for personalised LinkedIn outreach messages.

The design borrows the mechanic of a two-sided matching app deliberately: students complete the form because something useful comes back immediately, and the client receives pre-qualified, structured leads instead of raw applications.

---

## System Components

| Component | Description |
|-----------|-------------|
| Student form | Skill chips, free-text bio, profile fields — clean product-feel intake |
| Instant feedback | AI-generated feedback returned to the student on submit |
| Client dashboard | Filterable candidate view (level / country / type) with match scores |
| AI candidate summary | One-paragraph AI summary per candidate |
| Outreach generator | Modal that drafts a personalised LinkedIn message on demand |
| Automation glue | Make.com webhook → Airtable write (deferred — not yet wired) |

---

## Architecture

    Student submits profile (GitHub Pages form)
    → AI feedback generated and returned to student instantly
    → [planned] Make.com webhook fires → writes submission to Airtable
    → Client dashboard reads candidates, applies match scoring + AI summary
    → Client clicks "Write" → AI drafts personalised outreach message
    → Client copies and sends

---

## Key Design Decisions

- Instant AI feedback on submit — the value-return mechanic that drives form completion
- Two-sided design — student-facing form and client-facing dashboard in one single-file build, tabbed
- Preloaded demo data — dashboard appears populated immediately for presentation, before live data flows
- Standalone repo — kept separate from the main portfolio repo as its own deployable thing
- Match scoring and outreach generation kept on the client side of the experience, invisible to the student

---

## Rebuild-From-Prompt Protocol

**Accounts required:**
- [ ] GitHub (Pages enabled)
- [ ] Make.com (webhook + Airtable write)
- [ ] Airtable (candidate storage)
- [ ] Anthropic API (feedback, summaries, outreach drafting)

**Steps:**
1. Build the single-file HTML with two tabbed views (student form / client dashboard)
2. Wire the submit handler to the AI feedback call
3. Create the Airtable `TalentBridge` table matching the form fields
4. Build the Make.com scenario: webhook receives submission → writes to Airtable
5. Point the dashboard at Airtable for the live candidate pull
6. Preload demo records so the dashboard renders populated for presentation

---

## Known Constraints

- Automation layer (Make.com → Airtable) is deferred — current build runs on preloaded demo data, not live submissions
- AI feedback and outreach quality depend on the prompt and the model used
- No authentication — anyone with the URL can submit or view the dashboard in the demo state
- GDPR: live deployment handling real student data would need consent capture and a data-handling policy before going live

---

## Iteration Notes

- Initial build — two-sided microsite, AI feedback, scored dashboard, outreach generator, preloaded demo data, deployed to GitHub Pages
- Pending — Make.com automation wiring to move from demo data to live submissions
