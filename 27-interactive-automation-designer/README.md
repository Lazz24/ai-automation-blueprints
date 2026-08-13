# 27 — Magic Hat: Interactive Automation Designer

**In one line:** Describe a boring task you do by hand, and Magic Hat draws you a map of how to automate it — and tells you how much time and money you'd save.

**Live app:** https://interactive-automation-designer.laszlo-t-sandor.workers.dev/

---

## What it does

You type in a repetitive manual task. Magic Hat turns it into an automation blueprint — an interactive flowchart of the automated version — and calculates the payoff (time saved and money saved per month).

## How to use it

1. **Describe your task.** Type the manual process you're tired of doing into the sidebar.
2. **Build it.** Trigger the builder — it generates a flowchart of the automated workflow.
3. **Inspect any step.** Click a node in the chart to see what it does, its inputs/outputs, and optimization tips.
4. **See the payoff.** Enter how often you run the task and your hourly rate, and the ROI Estimator shows your projected monthly savings.

## Before you start

Magic Hat needs one of the following to generate workflows:

- **A free Groq API key** — add it in settings to generate real, custom workflows (uses the Llama-3.3-70b model).
- **Mock Mode** — flip this on to demo the app with preset examples, no key required.

> **Note:** In Mock Mode, node values like `tool1` and `allReviews` are placeholders, not real tools. They're there to show the shape of a workflow, not a working integration.

## The two views (and who they're for)

- **The flowchart + ROI number** — for anyone. "Here's your task, automated, and here's what it saves you." This is what to lead with when showing people.
- **The Node Inspector panel** — for the technically curious. Click a step to see how it's wired: its parameters and its input/output data contract. It's the engine bay — most people just want to see the car drive.

## What's inside

| File | Purpose |
|------|---------|
| `index.html` | The page structure |
| `style.css` | The look and layout |
| `app.js` | The logic — workflow generation, node inspector, ROI calculator |
| `README.md` | This file |

## Running it locally

It's a plain static web page — no build step, no install. Just double-click `index.html` to open it in your browser.

## Deploying

Deployed as a Cloudflare Worker. Re-deploy after edits to push changes live.
