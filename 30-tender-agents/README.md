# 30 — Tender Agents

> A fully agentic pipeline that finds public-sector tenders, reads their documents, scores them against a client's real capabilities, and writes a grounded, compliance-checked bid — with a human only at the final sign-off. Eleven agents, multi-client-safe, honesty enforced in code. Built and verified end-to-end on live Dutch government tenders.

**Status:** `Built — containerized, pre-launch` — proven end-to-end on real TenderNed data, packaged as a portable Docker image, ready to demo; remaining steps are commercial (engagement/DPA paperwork) not technical
**Stack:** Python · SQLite · sentence-transformers · tiktoken · Groq · Docker
**Tags:** `#agentic` `#a2a` `#llm` `#rag` `#procurement` `#multi-tenant` `#ai-engineering`

---

## What It Does

An agent-to-agent pipeline for public procurement. Governments publish tenders by machine; this consumes them by machine. One agent finds new tenders, one reads each tender's official PDF, one extracts the requirements, one scores how well they fit a specific client, one retrieves that client's real track record, one writes the bid, one checks every requirement is answered, and one packages the result for a human to review and lodge.

The design priority is trustworthiness: the system only claims what a client can genuinely evidence, flags gaps instead of hiding them, and cannot leak one client's material into another's bid. Honesty and confidentiality are enforced in code, not asserted in policy.

It sits in the Magic Hat product tree and reuses the RAG Engine's embedding-and-chunking approach (sentence-transformers + tiktoken) for its client-document retrieval layer.

---

## System Components

| Component | Description |
|-----------|-------------|
| Scout | Polls the tender portal (TenderNed) and pulls new tenders; dedupes |
| Fetcher | Downloads each tender's notice PDF and extracts the text |
| Shredder | Parses the (Dutch) document into requirements, deadlines, and conditions, each tied to its source text |
| Qualifier | Scores each tender against a client's win-profile on five axes, with a hard capability gate; ranks and filters |
| Librarian | Local RAG over the client's own capability documents — embeds, stores, and retrieves per client; zero tokens |
| Drafter | Writes the bid section by section, grounded in retrieved client material; flags anything it can't support |
| Compliance-checker | Verifies every mandatory requirement is answered; PASS / FAIL / REVIEW gate |
| Pricer | Estimates the effort and price of the tender work |
| Submitter | Packages the finished bid into a reviewable document; never submits |
| Orchestrator | Runs the intake chain as one command |
| Onboarding | Turns a new client's documents into their scoring profile and document library, behind a human-confirmation gate |

---

## Architecture

    Scout finds tender → Fetcher pulls the PDF → Shredder extracts requirements
    → Qualifier scores it against the client's profile → ranks / filters
    → (for good matches) Librarian retrieves the client's relevant capabilities
    → Drafter writes the bid, grounded only in what was retrieved
    → Compliance-checker verifies every requirement is answered
    → Submitter packages a reviewable bid → human reviews, signs, lodges

    The AI writes and judges; the code computes, stores, and enforces every guard —
    so the parts that must be reliable do not depend on the model behaving.

---

## Key Design Decisions

- Grounding enforced in code, not prompts — the Drafter can only claim something if the client's documents actually support it; a model that claims coverage without a real citation is code-overridden to a flagged gap. The system structurally cannot help a company lie in a bid.
- Capability is a gate, not a score — if the client can't do the work, the tender is ranked Low regardless of other factors. A precondition is modelled as a hard gate, not one weighted term among many.
- Coverage-aware scoring — an axis the model can't assess is excluded and the score re-weighted over what it can; a score of 0 is a real rating, never silently treated as "unknown."
- Multi-client isolation, four ways — a conflict guard refuses to work two sides of one tender; per-client document and profile storage; and retrieval that requires a client identity, so cross-client leakage is impossible by construction.
- LLM narrates, code computes — every arithmetic result, every guard, every persistence step is plain code; the model only reads and writes language.
- Human-gated submission — the system never submits; the client stays the registered, signing bidder.

---

## Validation

- Full pipeline proven end-to-end on live TenderNed tenders, from discovery to packaged bid
- Two real bids with two honest outcomes: one passing compliance, one correctly FAILED because the client lacked a required document — the system refused to fabricate it and flagged the gap
- Multi-client isolation proven under a real second client: a different firm run through the whole pipeline, drawing only on its own documents, with the conflict guard refusing a tender already assigned to the first client
- Scoring discriminates correctly: off-profile tenders (grazing, furniture, vehicles) sink; genuine matches rise
- 410 automated tests passing; container verified running fully air-gapped (`--network none`)

---

## Rebuild-From-Prompt Protocol

**Stack required:**
- [ ] Python 3.12
- [ ] SQLite (bundled)
- [ ] sentence-transformers (local embeddings, CPU) + tiktoken (chunking)
- [ ] Groq API (LLM for parsing, scoring, drafting, compliance)
- [ ] Docker (portable image, data on mounted volumes)

**Build sequence:**
1. Intake: Scout (poll portal) → Fetcher (pull PDFs) → Shredder (extract requirements); verify against real data at each step
2. Qualifier: five-axis scoring with a capability gate and coverage-aware re-weighting
3. Librarian: per-client local RAG — embed, store, retrieve, scoped by client id
4. Drafter: section-by-section generation, grounding enforced in code, atomic writes
5. Compliance-checker: requirement-by-requirement gate (PASS / FAIL / REVIEW)
6. Submitter: human-gated packaging with an unmissable not-ready banner
7. Onboarding + isolation: per-client profiles and corpora, conflict guard, human-confirmation gate
8. Containerize: offline-baked models, mounted data volumes, key passed at runtime

---

## Known Constraints

- Output language is not yet consistent run-to-run — a Dutch submission needs reliable Dutch output (refinement work)
- No won/lost feedback loop yet — the system can't yet learn from which bids succeed; that's the feature that would lift win-rate over time
- Groq free tier is rate-limited and single-model — a live service needs a paid tier and fallbacks
- One data-egress point remains: the Drafter's generation call sends retrieved chunks to the LLM — closed for a real client by a self-hosted model or a zero-retention contract
- Commercial, not technical, gating: engagement contract + GDPR data-processing agreement required before any real client data

---

## Iteration Notes

- Built one agent at a time, each verified against real database contents before the next was added — a discipline that caught several real bugs (a brittle grounding matcher, a score-vs-null conflation, a conflict guard that didn't fire, a corpus-isolation leak) that "looks done" would have missed
- Reuses the RAG Engine's embedding + chunking approach (Magic Hat product tree)
- Delivery path: managed service for a first warm client → client-side Docker deployment when security demands it → hosted product only if it scales
- Future direction — true A2A: the source side is already agent-to-agent; the next step is an inbound responder so buyers' own agents can query the system directly. Technically close; waits on agent-identity standards and procurement protocols maturing.
