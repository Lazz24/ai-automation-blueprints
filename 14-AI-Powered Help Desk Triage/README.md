# 14 — AI-Powered Help Desk Triage

> Incoming support emails classified, routed, logged, and Slack-alerted before a human touches them.

**Status:** `Live`
**Stack:** Zapier · OpenAI (GPT-4o-mini) · ClickUp · Airtable · Slack
**Tags:** `#automation` `#ai-prompt-engineering` `#zapier` `#airtable` `#clickup`

---

## What It Does

Incoming support emails are automatically classified by AI into urgency levels and categories, routed to the correct ClickUp list, logged to Airtable, and — for high-urgency tickets — an instant Slack alert is fired to the response team. Low-complexity tickets receive an AI-generated draft reply saved directly in the task.

No human touches the ticket until it is already triaged, routed, and ready to act on.

---

## System Components

| Component | Description |
|-----------|-------------|
| Trigger | Zapier Email Inbox — fires on every incoming support email |
| AI classification | OpenAI GPT-4o-mini — urgency, category, owner, draft reply |
| Logging | Airtable — every ticket logged before path split |
| Task routing | ClickUp — three lists mapped to High / Medium / Low urgency |
| Alerting | Slack — immediate alert to #help-desk-alerts on High urgency |

---

## Architecture

    Support Email Received
            ↓
    Zapier Email Inbox (trigger)
            ↓
    OpenAI GPT-4o-mini
    — classifies urgency, category, owner
    — generates draft reply for Low urgency tickets
            ↓
    Airtable — logs every ticket to Ticket Log
            ↓
    Paths split by urgency
            ↓
    🔴 High   → ClickUp High Priority List + Slack alert to #help-desk-alerts
    🟡 Medium → ClickUp Medium Priority List
    🟢 Low    → ClickUp Low Priority List (draft reply included in task)

---

## AI Classification Output

| Field | Values |
|-------|--------|
| Urgency | High / Medium / Low |
| Category | Billing / Technical / Account / General |
| Summary | One sentence description of the issue |
| Owner | billing-team / tech-team / account-team / general-team |
| Draft Reply | Auto-generated for Low urgency tickets only |

---

## Airtable Schema

Base: `Help Desk Triage` — Table: `Ticket Log`

Fields: Timestamp · From · Subject · Urgency · Category · Summary · Owner · Draft Reply · Status

---

## Key Design Decisions

- Code by Zapier used over AI by Zapier — direct OpenAI API calls are more reliable and avoid native Zapier AI authentication issues
- Airtable logging happens before the path split — every ticket is recorded regardless of urgency routing
- Fallback handling built into the code step — if the OpenAI call fails, the ticket defaults to Medium/General so nothing is silently dropped
- GPT-4o-mini chosen over GPT-4o — sufficient for classification tasks at a fraction of the cost

---

## Rebuild-From-Prompt Protocol

**Accounts required:**
- [ ] Zapier (paid — multi-step Zap with Code by Zapier)
- [ ] OpenAI API
- [ ] ClickUp
- [ ] Airtable
- [ ] Slack

**Steps:**
1. Set up Zapier Email Inbox as trigger
2. Add Code by Zapier step — OpenAI API call with classification prompt, return JSON
3. Add Airtable step — Create Record in Ticket Log (runs before path split)
4. Add Zapier Paths — three branches based on urgency field from OpenAI output
5. High path: ClickUp task in High Priority list + Slack message to alert channel
6. Medium path: ClickUp task in Medium Priority list
7. Low path: ClickUp task in Low Priority list with draft reply in task description
8. Test with sample emails across all three urgency levels before publishing

---

## Potential Extensions

- Auto-send draft replies for Low urgency tickets via Gmail
- Escalation reminders if High tickets remain unresolved after a defined time window
- Weekly digest report generated from Airtable ticket data
- Client-facing demo interface — paste an email, see the triage result instantly

---

## Known Constraints

- Classification accuracy depends on email content quality — very short or ambiguous emails may misclassify
- Draft replies are generated for Low urgency only — Medium and High require human drafting
- No deduplication — forwarded or replied emails may create duplicate Airtable records

---

## Iteration Notes

- `2026-03` — Initial build and live deployment
