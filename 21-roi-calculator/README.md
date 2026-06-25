# 21 — Automation ROI Calculator

> A free, client-side calculator that turns one repetitive task into an annual cost figure and an automation payback period. A lead-generation tool.

**Status:** `Live` — single-file calculator on GitHub Pages
**Stack:** Vanilla JS · HTML/CSS · GitHub Pages
**Tags:** `#lead-generation` `#roi` `#calculator` `#client-side` `#dutch-design`

---

## What It Does

A self-contained ROI calculator aimed at prospects. The user enters one repetitive task — hours per week, number of people, fully-loaded hourly cost, and current automation level — and the tool returns the annual cost of doing it manually, the projected annual savings after automation, hours freed per year, and a payback period against a typical project cost. It ends with a branded verdict and a soft call-to-action.

All calculation runs in the browser — there is no backend, no API, and no data leaves the page. It is a deterministic estimator, not an AI tool, built as a top-of-funnel lead-generation asset that frames the cost of inaction in concrete euro terms.

---

## System Components

| Component | Description |
|-----------|-------------|
| Frontend | Single-file HTML/CSS/JS — Magic Hat / Dutch design language (navy, orange, red) |
| Inputs | Task name, hours/week, people, hourly cost, current automation level |
| Calculation | Client-side JavaScript — no server, no API |
| Outputs | Annual cost, annual savings, hours freed, payback period, verdict |
| CTA | Soft call-to-action linking to the consulting engagement |

---

## Calculation Logic

    annual hours   = hours/week × people × 48 working weeks
    annual cost    = annual hours × hourly rate
    addressable    = annual cost adjusted for current automation level
    annual savings = 78% of addressable cost
    payback        = typical project cost ÷ annual savings, expressed in weeks

The verdict text adapts to the selected automation level (none / partial / mostly automated), reframing the result for each case.

---

## Key Design Decisions

- Fully client-side — no backend, no API key, no data collection; the page is self-contained and private by default
- Deterministic, not AI — the value is a fast, transparent estimate, not a generated narrative
- Payback computed against the consulting project price range — the tool is a sales mechanic that connects the cost of manual work to the cost of fixing it
- Dutch design language — navy/orange/red palette, Playfair Display + DM Sans, consistent with the Magic Hat brand
- Built-in assumptions kept fixed for simplicity — a single-button experience the prospect can complete in seconds

---

## Rebuild-From-Prompt Protocol

**Accounts required:**
- [ ] GitHub (Pages enabled)

**Steps:**
1. Build the single-file HTML with the five input fields
2. Implement the client-side calculation (annual hours → cost → savings → payback)
3. Add the adaptive verdict logic for each automation level
4. Style to the Magic Hat / Dutch design language
5. Add the soft CTA and deploy to GitHub Pages

---

## Known Constraints

- The model uses fixed built-in assumptions, not user-configurable ones:
  - 48 working weeks per year
  - 78% savings on the addressable (manual) workload
  - A typical project cost used as the payback denominator
  - Hourly rate pre-filled with a default (editable by the user)
- These assumptions are deliberately tuned for a lead-generation estimate — the output is indicative, not a formal business case
- No data is stored — results exist only in the browser session

---

## Iteration Notes

- Initial build — single-file client-side calculator, adaptive verdict, Dutch design language, deployed to GitHub Pages
- Planned — configurable assumptions (working weeks, savings %, project cost) exposed as user inputs, replacing the current hardcoded values
