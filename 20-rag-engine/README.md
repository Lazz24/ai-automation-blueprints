# 20 — RAG Engine

> A retrieval-augmented generation engine that answers questions grounded strictly in an uploaded document corpus — with adaptive scoring and a two-gate refusal model to prevent hallucination. Built and stress-tested locally; deploying to the Magic Hat product tree.

**Status:** `Built — deploying` — proven locally, scheduled for deployment to `ragengine.magichat.consulting` as part of a unified product-tree launch
**Stack:** Python · FastAPI · PostgreSQL + pgvector · sentence-transformers · Groq
**Tags:** `#rag` `#llm` `#vector-search` `#fastapi` `#postgres` `#ai-engineering`

---

## What It Does

A retrieval-augmented generation engine. Documents are embedded into a vector store; user questions are matched against that store and answered by an LLM that is constrained to ground its answer in the retrieved passages. The design priority is trustworthiness: the engine refuses to answer when the corpus does not support a question, rather than inventing a plausible response.

It is the foundational layer of the Magic Hat product tree — other products push knowledge into it and query it.

---

## System Components

| Component | Description |
|-----------|-------------|
| Embedding layer | sentence-transformers (768-dim) — converts documents and queries into vectors |
| Vector store | PostgreSQL with pgvector — similarity search over the corpus |
| Retrieval scoring | Distribution-shape adaptive scoring with per-document threshold and top-k overrides |
| LLM | Groq (llama-3.3-70b-versatile) — narrates retrieved content, never invents it |
| Auth | Passphrase-gated upload and query endpoints |
| API | FastAPI service exposing upload and query |

---

## Architecture

    Document uploaded → chunked → embedded → stored in pgvector
    User asks a question → question embedded
    → similarity search retrieves candidate passages
    → adaptive scoring gate filters by relevance
    → if nothing clears the gate: refuse (no answer invented)
    → if passages clear: LLM narrates an answer grounded in them
    → answer returned with source grounding

---

## Key Design Decisions

- Two-gate refusal model — a scoring gate catches unrelated queries; an LLM gate catches topically adjacent but unanswerable ones. Each gate does its own job.
- Anti-hallucination as a hard architectural constraint — the LLM narrates already-retrieved content and never performs reasoning that fabricates facts
- Distribution-shape adaptive scoring — outperforms a single fixed similarity threshold across diverse document corpora
- Per-document score_threshold and top_k overrides — different corpora need different retrieval sensitivity
- Standalone-first — the engine is fully functional on its own; bridges to other products are added later, not baked in

---

## Validation

- Passed fast evaluation (20/20) and full evaluation (13/13) harnesses
- Stress-tested across corpus boundary cases, off-topic queries, and refusal conditions

---

## Rebuild-From-Prompt Protocol

**Stack required:**
- [ ] Python 3.12 + FastAPI
- [ ] PostgreSQL with pgvector
- [ ] sentence-transformers (embedding model)
- [ ] Groq API (LLM narration)

**Build sequence:**
1. Stand up the Postgres + pgvector store
2. Build the embedding + upload path (chunk → embed → store)
3. Build the retrieval path (embed query → similarity search → adaptive scoring gate)
4. Add the LLM narration step, constrained to retrieved passages
5. Add the second (LLM) refusal gate for adjacent-but-unanswerable queries
6. Gate the endpoints behind a passphrase
7. Eval-gate with a fast and a full test harness before shipping

---

## Known Constraints

- Currently runs locally — public deployment pending the product-tree droplet launch
- Answer quality is bounded by corpus quality — a thin corpus yields more refusals (by design)
- Groq free tier is rate-limited
- Single-corpus retrieval — multi-tenant separation is a future consideration

---

## Iteration Notes

- Built and eval-gated locally (fast 20/20, full 13/13)
- Bridge A (ETL Pipeline → RAG) connected — receives pushed reports into the corpus
- Bridge C (Wayfinder ↔ RAG) connected — bidirectional query/answer exchange
- Planned — deployment to `ragengine.magichat.consulting` in the unified tree launch
