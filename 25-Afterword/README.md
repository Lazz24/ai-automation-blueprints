# Sitdown

Turns raw meeting notes into a list of who does what by when.

Paste the notes. Get a table: task, owner, deadline, priority, context.
Edit it. Export it. Done in about a second.

## What it does

- Takes pasted text or a dropped `.txt` / `.md` file
- Extracts action items: task, owner, deadline, priority, context, confidence
- Never invents an owner — unclear ownership comes back as `unassigned`
- Never invents a date — the model reports the deadline as it was said
  ("next Friday", "before the board meeting"); the resolver turns that
  into a calendar date in code, or leaves it as the raw phrase if it can't
- Inline editing before export
- Exports: CSV, Markdown, JSON
- Operator dashboard: run history, latency, failure classes, extraction quality

## Why the dates are done that way

The model narrates. The code computes. An LLM asked to calculate a date
will produce a plausible one whether or not it's right, and a wrong deadline
in a client's task list is worse than no deadline. So the model copies the
words, and `dates.py` does the arithmetic — 27 cases, tested. Anything it
can't resolve with confidence stays as the phrase that was actually said.

## Stack

Python 3.12 · FastAPI · PostgreSQL 17 · Groq (llama-3.3-70b-versatile)

## Run it

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt
psql -U postgres -c "CREATE DATABASE meeting_notes;"
psql -U postgres -d meeting_notes -f schema.sql
psql -U postgres -d meeting_notes -f migrate_001.sql
python main.py
```

`.env`:
