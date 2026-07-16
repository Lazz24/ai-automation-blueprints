# llm-eval-harness

A hand-built test runner for LLM outputs. Cases in YAML, run against a pluggable
target, asserted, scored across repeated runs, diffed against the last run.

No eval library. Built from scratch so the design decisions are mine to defend.

## Why

Every AI system that makes a claim someone can be audited on — a grant amount, a
compliance verdict, an invoice total — needs a way to prove the claim is right.
"The model said so" is not an answer an auditor accepts.

This harness exists to answer one question: **what are the guardrails worth?**

It runs the same ten test cases against two implementations of the same job:

- **`funding_engine`** — the guarded path. The LLM extracts raw values and the
  verbatim source span each came from. Python does all arithmetic, all threshold
  comparisons, all eligibility logic. The LLM never computes and never decides.
- **`raw_llm`** — the control. One call, the whole job. Same document, same
  applicant data, same rules stated in plain language, same output fields, same
  request for source spans. Everything the guarded path gets except the
  architecture.

The delta between them is the argument.

## Results

Suite: `funding_eligibility`, 10 cases, 3 runs each, `llama-3.3-70b-versatile`,
temperature 0.

| Target | Pass rate | Fails |
|---|---|---|
| `funding_engine` | **10/10** | — |
| `raw_llm` | **8/10** | FE-006, FE-009 |

Eight of ten cases, a well-prompted single call does the job correctly. That is
the honest number and it is smaller than I expected. The interesting part is
*which* two it fails.

**FE-006 — arithmetic.** The document states a 35% rate and a €742,000 cost cap.
The answer is €259,700. `raw_llm` returned 259,170 on one run and 259,200 on
another — at temperature 0, which is nominally deterministic. It is not that the
model cannot multiply. It is that it multiplies *inconsistently while sounding
certain*. A single-run harness would have shown one plausible wrong number and I
would never have known it moves. `funding_engine` extracts `0.35` and `742000`
and lets Python do the rest; it cannot get this wrong.

**FE-009 — boundaries.** Headcount is exactly 250. The SME rule, given to
`raw_llm` verbatim, says *fewer than* 250. It said eligible anyway. Stated rules
get approximated. Coded rules do not.

Every `cites_source` assertion passed on both targets. Provenance was not the
differentiator — I expected it to be and I was wrong. See "What I got wrong".

## What the harness caught in my own code

**A 2.5× error in the guarded path.** FE-005: "40% of eligible costs, costs
capped at €625,000" → the grant ceiling is €250,000. The extraction step filed
the €625,000 *cost cap* as the *grant ceiling*, so the derivation never ran and
the answer came out 2.5× high. A cap on what an applicant may **spend** is not a
figure for what they may **receive** — the prompt had never said so.

The fix was to the extraction contract, not the arithmetic, because the
arithmetic was never in question — the LLM never touched it. The fix then passed
FE-006, a case it wasn't written for, in a different language. That is how I know
it encoded a real domain rule rather than chasing one test.

This is the harness earning its existence. The bug was in my system, it was
plausible, and nothing else would have surfaced it.

## What I got wrong

**The control group was rigged and scored 0/10.** The first version of `raw_llm`
returned an empty `_sources` map by construction — it was never asked for
citations, then failed for not providing them. Six of ten failures were that
artifact. A control that cannot pass is not a control, and a 10-vs-0 delta built
on one is the kind of thing that reads as fraud when someone opens the file.

Fixed: `raw_llm` is now given the rules, the exact reason strings, and an explicit
request for verbatim spans. It scored 8/10. My headline got 80% smaller and 100%
more true. The two remaining failures carry the argument on their own.

**Predicted `raw_llm` would score 3–6/10.** It scored 8. Predicted it would
invent or paraphrase source spans. It cited accurately. Both wrong, both on
record above.

## Design decisions

**Three runs, all must pass.** LLMs are nondeterministic, `temperature=0`
notwithstanding, and the harness assumes that isn't sufficient — because it
isn't (see FE-006). A case passing 2/3 is a failing case. Flaky is not passing.

**Errored is not failed.** A case whose target raised — API timeout, rate limit,
malformed response — has no verdict. Nothing was asserted, so nothing was proven
either way. Errored cases are excluded from the pass rate, excluded from the
regression diff in both directions, and reported separately. `run.py` exits 2 on
an errored run: inconclusive must not be indistinguishable from green.

This was not in the original design. It surfaced when a Groq daily token limit
killed six cases mid-run and the diff reported six regressions. Nothing had
regressed. A diff that lies is worse than no diff.

**The LLM narrates, code computes.** All arithmetic, thresholds and comparisons
live in `harness/rules.py` — an importable module with no LLM dependency,
testable on its own. The extraction prompt is forbidden to compute anything.

**`cites_source` is verified, not trusted.** The assertion checks that the span
is non-empty *and* appears in the input document. Whitespace is normalized —
line wrapping is an artifact of the document, not of the model's citation — but
nothing else is. One digit off still fails. That normalization is a real
concession and it is where I drew the line; normalizing case or punctuation
would start letting invented spans through.

**Flat output contract.** `{field: value, ..., "_sources": {field: span}}`.
Sources are keyed by field rather than attached to values, because a *derived*
value has no source span — there is no text to cite. FE-005 and FE-006 cite
their **inputs** (`funding_rate`, `max_project_budget`) rather than the computed
answer. Citing a number that was never written down would be a lie.

**Separation.** `target.py` knows nothing about assertions. `assertions.py` knows
nothing about targets or Groq. `runner.py` is the only module that knows about
n-runs. `report.py` and `diff.py` only read result structures. If any of those
start importing each other sideways, the design is broken.

## Structure
