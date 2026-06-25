# 11 — Portfolio Infrastructure System

> GitHub mono-repo design, documentation standard, and rebuild-from-prompt protocol applied across all projects.

**Status:** `Active`
**Stack:** GitHub · Markdown · JSON
**Tags:** `#infrastructure` `#github`

---

## What It Does

The infrastructure system for this entire repository. Covers the mono-repo design, folder hierarchy, naming conventions, .gitignore security structure, JSON storage model, project documentation standard, and the rebuild-from-prompt protocol that every project folder follows.

---

## System Components

| Component | Description |
|-----------|-------------|
| Mono-repo design | Single repo housing all projects with indexed folder structure |
| Naming conventions | `{index}-{slug}` pattern for all folders and files |
| .gitignore policy | Security exclusions for credentials, keys, and OS files |
| Documentation standard | README template applied consistently across all projects |
| Rebuild-from-prompt | Protocol for reconstructing any project from its documentation |
| Tagging system | Function-based and stack-based tags for discoverability |

---

## Naming Convention

    {two-digit-index}-{descriptive-slug}/

- All lowercase, hyphen-separated
- Max 4 meaningful words in slug
- Index enforces display order in GitHub

---

## Documentation Standard

Every project README contains:
- Status, stack, and tags in the header
- What It Does — plain description, no marketing language
- System Components — table format
- Architecture — flow diagram or numbered steps
- Key Design Decisions — constraints and rationale
- Rebuild-From-Prompt Protocol — step-by-step reconstruction guide
- Known Constraints — honest about what it does not handle
- Iteration Notes — changelog, newest first

---

## Rebuild-From-Prompt Protocol

**Files in this folder:**
- `schemas/` — JSON storage model
- `notes/` — rebuild-from-prompt protocol detail, .gitignore policy

---

## Known Constraints

- Documentation is only as current as the last update
- Rebuild protocol assumes accounts and API access are available

---

## Iteration Notes

- `2026-03` — Repo rebuilt clean — removed non-owned projects, added BrandGPT
- `2025-03` — Initial repo structure built
