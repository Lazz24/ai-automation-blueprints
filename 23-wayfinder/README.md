# 23 — Wayfinder · Conversational RAG

> A conversational retrieval system with multi-turn memory, GDPR consent handling, and live web enrichment — that guides a user through a knowledge domain and falls back to the RAG Engine when its own corpus can't answer. Built and proven locally; deploying to the Magic Hat product tree.

**Status:** `Built — deploying` — proven locally, scheduled for deployment to `wayfinder.magichat.consulting` as part of a unified product-tree launch
**Stack:** Python · FastAPI · PostgreSQL · Groq · Serper · Airtable
**Tags:** `#conversational-rag` `#llm` `#multi-turn-memory` `#gdpr` `#fastapi` `#ai-engineering`

---

## What It Does

A conversational layer over a knowledge corpus. Where the RAG Engine answers single questions, Wayfinder holds a multi-turn conversation — it remembers context across turns, handles consent explicitly, enriches answers with live web data when useful, and guides the user toward what they actually need. When its own corpus can't answer, it silently queries the RAG Engine in the background and returns that answer seamlessly.

The "· Conversational RAG" suffix is kept deliberately — it signals the technical category clearly to a technical audience.

---

## System Components

| Component | Description |
|-----------|-------------|
| Conversation engine | Multi-turn memory across a session |
| Consent layer | GDPR consent banner with multiple consent levels governing what is stored |
| Intent classification | Routes each message (answerable / conversational / unclear) |
| Corpus retrieval | RAG over Wayfinder's own demo corpus |
| Live enrichment | Serper web search for current information |
| Data store | PostgreSQL (multi-table — conversations, messages, profiles, consent) |
| Bridge B | Pushes structured conversation profiles to Airtable |
| Bridge C | Bidirectional with the RAG Engine — falls back to it, and pushes resolved profiles back as PDFs |

---

## Architecture

    User message → consent level checked
    → intent classified (answerable / conversational / unclear)
    → retrieval over Wayfinder's corpus
    → if corpus can't answer: silently query the RAG Engine (Bridge C)
    → optional live enrichment via web search (Serper)
    → LLM responds, grounded, with multi-turn context carried forward
    → on resolve: conversation profile pushed to Airtable (Bridge B)
      and as a PDF back to the RAG Engine (Bridge C)

---

## Key Design Decisions

- Consent-first — GDPR consent levels govern what gets stored; none-consent users generate no conversation or message records
- Multi-turn memory — context carries across turns so the conversation actually progresses
- Silent RAG fallback — when Wayfinder's corpus fails, it queries the RAG Engine invisibly rather than refusing, so the user experience stays smooth
- Anti-hallucination carried over from the RAG Engine — answers stay grounded; the system was stress-tested against a class of inference-from-suggestion hallucinations and hardened against them
- Standalone-first — fully functional alone; the Airtable and RAG bridges are additions

---

## Validation

- Stress-tested across multi-turn memory (5 turns), all consent levels, Dutch-language input, off-topic redirection, frustrated-user tone, rapid short messages, long complex messages, and corpus boundary cases
- Bridge C verified bidirectionally — fallback answers retrieved from the RAG Engine corpus, and resolved profiles pushed back as PDFs

---

## Rebuild-From-Prompt Protocol

**Stack required:**
- [ ] Python 3.12 + FastAPI
- [ ] PostgreSQL
- [ ] Groq API (responses)
- [ ] Serper (live enrichment)
- [ ] Airtable (profile push)

**Build sequence:**
1. Database schema — conversations, messages, profiles, consent
2. Consent layer and banner, with consent-level gating on storage
3. Intent classifier
4. Corpus retrieval (RAG over the demo corpus)
5. Multi-turn memory across the session
6. Live enrichment via web search
7. Bridge B (Airtable) and Bridge C (RAG Engine, bidirectional)
8. Stress-test across memory, consent, language, and tone cases before shipping

---

## Known Constraints

- Currently runs locally — public deployment pending the product-tree droplet launch
- Live enrichment quality depends on the search provider's results
- Groq free tier is rate-limited
- Corpus is a demo vertical — production use needs a domain-specific corpus

---

## Iteration Notes

- Built standalone, then bridged — Bridge B (Airtable) and Bridge C (RAG Engine) added after the core was proven
- Four bugs fixed during stress testing: a consent-mode constraint, an intent-classifier misroute, an inference-from-suggestion hallucination, and a none-consent record leak
- Planned — deployment to `wayfinder.magichat.consulting` in the unified tree launch
