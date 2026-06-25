# 07 — LinkedIn Connection Scaling System

> Connection-first outreach model. Message sequence triggers only on acceptance.

**Status:** `Active`
**Stack:** Apollo · LinkedIn · Zapier
**Tags:** `#outreach-system` `#linkedin` `#apollo` `#zapier`

---

## What It Does

A structured outreach system built on a connection-first model. Sending a message is not the first action — connection request is. Acceptance triggers the message sequence. Apollo handles lead sourcing and export volume; Zapier handles intelligence augmentation on acceptance events. Includes saved search expansion logic for broadening the lead universe beyond a narrow vertical.

---

## System Components

| Component | Description |
|-----------|-------------|
| Connection strategy | Request-first, message on acceptance only |
| Lead sourcing | Apollo export with volume and filter logic |
| Saved search expansion | Prompts and logic for broadening targeting beyond ESG |
| Acceptance trigger | Zap fires on connection acceptance event |
| Intelligence augmentation | Enrichment runs on accepted connection before message |
| Volume handling | Structure for managing high lead volume without rate limits |

---

## Architecture

    Apollo exports lead list
    → Connection requests sent
    → Acceptance event triggers Zap
    → Enrichment runs on accepted connection
    → Message sequence initiated with enriched context

---

## Key Design Decisions

- Message only on acceptance — avoids LinkedIn restrictions and improves reply rates
- Enrichment runs after acceptance — avoids wasting API calls on non-responders
- Saved search expansion designed to widen funnel without losing relevance
- Volume structure designed to stay within LinkedIn daily limits

---

## Rebuild-From-Prompt Protocol

**Accounts required:**
- [ ] Apollo.io
- [ ] LinkedIn
- [ ] Zapier

**Files in this folder:**
- `workflows/` — Zap-triggered augmentation flow
- `notes/` — connection-first strategy rationale, saved search expansion prompts, volume limits

---

## Known Constraints

- LinkedIn rate limits change — volume structure needs periodic review
- Acceptance event detection depends on method used

---

## Iteration Notes

- `2025-01` — Added saved search expansion logic
- `2025-01` — Initial build
