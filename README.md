# AI Automation Portfolio
### László Sándor

AI automation systems, GTM infrastructure, and operational tooling — built on Apollo, Zapier, OpenAI, Airtable, n8n, Make.com, Groq, Cloudflare Workers, Supabase, and more.

**CSRD Compass:** [lazz24.github.io/ai-automation-portfolio/13-csrd-compass](https://lazz24.github.io/ai-automation-portfolio/13-csrd-compass/)
**Net Zero Roadmap:** [lazz24.github.io/ai-automation-portfolio/15-net-zero-roadmap](https://lazz24.github.io/ai-automation-portfolio/15-net-zero-roadmap/)
**Pain → Blueprint Engine:** [lazz24.github.io/ai-automation-portfolio/19-Pain-Blueprint-Engine](https://lazz24.github.io/ai-automation-portfolio/19-Pain-Blueprint-Engine/)

---

## How This Repo Is Structured

Each project folder contains a README documenting:
- What the system does
- Full architecture and pipeline
- Key design decisions
- Schema where applicable
- Rebuild-from-prompt protocol
- Known constraints and iteration notes

Blueprint level only. Actual prompts, schemas, and code live in a private repository.

---

## Projects

| # | Project | Stack | Status |
|---|---------|-------|--------|
| [01](./01-funding-intelligence-engine/) | Funding Intelligence Engine | Groq · Airtable · GitHub Pages · Vanilla JS | 🟢 Live |
| [02](./02-b2b-lead-qualification/) | B2B Lead Qualification Pipeline | Apollo · Zapier · OpenAI · Airtable | 🟢 Production |
| [03](./03-executive-intelligence-engine/) | Executive Intelligence Engine | Apollo · OpenAI · Zapier · Airtable · Slack | 🔵 Active |
| [04](./04-grant-monitor-bot/) | Grant Monitor Bot | Zapier · ClickUp · OpenAI | 🔵 Active |
| [05](./05-techport-radar-scoring/) | TechPort Radar & Signal Scoring Engine | Airtable · Zapier · Apollo | 🔵 Active |
| [06](./06-n8n-operational-automation/) | n8n Operational Automation Systems | n8n · JSON · POS data · Webhooks | 🔵 Active |
| [07](./07-linkedin-connection-scaling/) | LinkedIn Connection Scaling System | Apollo · LinkedIn · Zapier | 🔵 Active |
| [08](./08-hunter-harvest-gtm/) | Hunter vs Harvest GTM Structuring | Apollo · Airtable · Zapier | 🔵 Active |
| [09](./09-funding-dashboard-architecture/) | Funding Action Items Dashboard Architecture | ClickUp · Zapier · Airtable | 🟡 Design |
| [10](./10-streamlit-realestate-poc/) | Streamlit Real Estate Intelligence POC | Streamlit · Python · Local | 🟣 POC |
| [11](./11-portfolio-infrastructure/) | Portfolio Infrastructure System | GitHub · Markdown · JSON | 🔵 Active |
| [12](./12-brandgpt-positioning/) | BrandGPT / Personal Positioning System | AI Prompting · Brand Strategy · LinkedIn | 🔵 Active |
| [13](./13-csrd-compass/) | CSRD Compass — Sustainability Readiness Tool | Make.com · Groq · Airtable · Notion · Gmail · HTML/JS | 🟢 Live |
| [14](./14-AI-Powered%20Help%20Desk%20Triage/) | AI-Powered Help Desk Triage | Zapier · OpenAI · ClickUp · Airtable · Slack | 🟢 Live |
| [15](./15-net-zero-roadmap/) | Net Zero Roadmap Generator | Groq · Cloudflare Workers · Supabase · jsPDF · GitHub Pages | 🟢 Live |
| [16](./16-Magic%20Hat%20ESG%20Intelligence%20Engine/) | Magic Hat ESG Intelligence Engine | ActivePieces · Google Sheets · Groq · Resend · QuickChart | 🟡 In Development |
| [17](./17-magic-hat-radar/) | Execution Radar (Client Build) | Airtable · Zapier · ClickUp · Slack · Groq | 🔵 Active |
| [18](./18-magichat-talentbridge/) | Magic Hat TalentBridge | GitHub Pages · Make.com · Airtable · Anthropic API | 🟣 Demo |
| [19](./19-Pain-Blueprint-Engine/) | Pain → Blueprint Engine | Make.com · Groq · GitHub Pages · Vanilla JS | 🟢 Live |
| [20](./20-rag-engine/) | RAG Engine | Python · FastAPI · PostgreSQL + pgvector · Groq | 🟠 Built — deploying |
| [21](./21-roi-calculator/) | Automation ROI Calculator | Vanilla JS · HTML/CSS · GitHub Pages | 🟢 Live |
| [22](./22-etl-pipeline/) | ETL Data Pipeline | Python · FastAPI · PostgreSQL · pandas · Groq | 🟠 Built — deploying |
| [23](./23-wayfinder/) | Wayfinder · Conversational RAG | Python · FastAPI · PostgreSQL · Groq · Serper · Airtable | 🟠 Built — deploying |
| [24](./24-llm-eval-harness/) | LLM Eval Harness | Python 3.12 · Groq · PyYAML · Hand-built (no eval library) | ⚪ Built — local |
| [25](./25-sitdown/) | Sitdown · Meeting Notes → Action Items | Python 3.12 · FastAPI · PostgreSQL 17 · Groq | 🟠 Built — deploying |
| [26](./26-mini-nms/) | Mini-NMS — Network Operations Center | Python (stdlib) · Cisco IOS parsing · Vanilla JS · Render | 🟢 Live |
| [27](./27-interactive-automation-designer/) | Interactive Automation Designer | Vanilla JS · HTML/CSS · Groq · Cloudflare Workers | 🟢 Live |
| [28](./28-longgame-fpl/) | LongGame — FPL Decision Engine | Python · FastAPI · SQLite · PuLP · HTMX · Render | 🟢 Live |
---

## Stack

| Layer | Tools |
|-------|-------|
| AI / LLMs | OpenAI API · Groq (Llama 3.3 70B) · Anthropic Claude |
| Automation | Zapier · Make.com · n8n · ActivePieces |
| Data / CRM | Airtable · Notion · ClickUp · Google Sheets · Supabase |
| Lead sourcing | Apollo.io · LinkedIn |
| Backend / Data | Python · FastAPI · PostgreSQL · pgvector · pandas |
| Interfaces | GitHub Pages · Cloudflare Workers · Streamlit · Vanilla JS · HTMX · SQLite |
| Communication | Slack · Gmail · Resend |
| Infrastructure | GitHub · JSON · Markdown |
| Charts & Reporting | QuickChart · jsPDF |
| Testing / Eval | Hand-built eval harness (PyYAML · Groq · assertion + regression diff) |
| Optimization | PuLP (CBC linear programming) |

---

## Design Principles

**LLM narrates, code computes**
No LLM performs arithmetic, threshold comparison, or eligibility judgement. The model extracts values and the verbatim span each came from; Python does the rest. Enforced in code, not in prompts — and verified by an eval harness ([24](./24-llm-eval-harness/)) that runs the same suite against an unguarded control to measure what the guardrails are actually worth. Sitdown ([25](./25-sitdown/)) is the clearest case: the model reports a deadline as it was said — "next Friday", "before the board meeting" — and a tested resolver turns that into a date, or leaves the phrase alone when it can't.

**Classify failures, don't lump them**
A dashboard that reports "failed" for both a broken extraction and an exhausted API quota is reporting a healthy system as broken. Failure classes are distinguished at the storage layer, not just in logs.

**One-button UX for client tools**
Decision makers get a single action. The pipeline complexity is invisible. Demo interfaces are always built separately from production backends.

**Rebuild-from-prompt standard**
Every project is documented so it can be fully reconstructed from its prompt set, architecture notes, and tool configuration — without access to the original build.

**Constraint-first design**
Systems are built around real limitations: free-tier APIs, no-code platform restrictions, budget and time pressure. Constraints produce cleaner architecture.

**Separate demo from ops**
Client-facing interfaces are always built separately from production backends. The operational system handles real data; the demo handles presentations.

**Standalone-first, bridge later**
Each product is built and proven on its own before being connected to others. Bridges add a compounding intelligence layer without turning any product into a monolith.

---

## Status Key

| Badge | Meaning |
|-------|---------|
| 🟢 Live / Production | Deployed and running |
| 🔵 Active | Built and operational |
| 🟠 Built — deploying | Built and proven locally; deployment pending |
| ⚪ Built — local | Runs locally by design; no deployment path intended |
| 🟡 Design / In Development | Architecture complete or build in progress |
| 🟣 Demo / POC | Working demo or proof of concept, pre-production |

---

*All systems documented to a rebuild-from-prompt standard — any project can be fully reconstructed from its architecture notes and prompt set.*
