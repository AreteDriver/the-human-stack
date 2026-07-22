# Case Study #005: The First Live Run of an Eval Harness

## Evidence

**Level:** E4
**Confidence:** High
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-05-23
**Review Trigger:** Production eval deployment against live model output
**Expected Upgrade:** E5
**Owner:** @AreteDriver

---

## Problem

An LLM eval harness had 391 passing self-tests. It had caught nothing. This is not a contradiction — it is a category error.

The harness tested its own metric classes, scoring math, and CLI. It had never run against real, messy, production model output. Eval *engineering* (building the framework) and eval *practice* (running it against reality) are not the same thing. The gap between them is where false confidence lives.

## Context

The suite — `benchgoblins-ask` — guarded a shipped fix for a fantasy-sports product. The fix (commit `5c2cf48`) resolved a regression where raw waiver-shape JSON leaked into the chat `rationale` field instead of being mapped into the response envelope.

The suite was 20 cases with six `fail_fast` structural gates — regex checks that catch the leak shape without subjectivity. Any one gate failing forces the case to FAIL regardless of composite score.

### Wiring Friction

Three failures before a single result:

1. Harness API key was dead — a 401 flagged 12 days earlier in memory, never acted on.
2. Adapter imports product code in-process; harness venv was missing `prometheus_client`.
3. Only then — using the product's own working key — did 20 real calls go through.

This is the unglamorous majority of eval work. The metrics are the easy part.

## Result

15 of 20 passed. Five failed — all the same shape: `waiver_*` cases, all tripping one `regex_absence` gate at 0%.

**First read:** *The eval caught a live regression.*

**Actual diagnosis:** The gate's pattern `"rationale"\s*:\s*"(?:[^"\\]|\\.)*recommendations` matched the plain English word "recommendations" in ordinary prose, not the escaped JSON key it was built to detect.

No regression. The fix was holding. The bug was in the eval metric.

## Fix

Anchor the pattern to the escaped-JSON-key shape:

```
"rationale"\s*:\s*"(?:[^"\\]|\\.)*\\"recommendations\\"\s*:
```

Verification: applied new pattern to the 20 stored responses from the original run. Same inputs, only the metric changed: **0/20 false positives, down from 5/20.** No false negatives.

## Lesson

> The first live run of an eval harness mostly finds bugs in the eval harness. That is not the harness failing — that is the harness finally earning the right to be trusted.

A metric never run against real production output is a hypothesis, not a guardrail. This was a reasonable hypothesis — "a leaked JSON key contains the word `recommendations`" — that was true and useless, because so does every sentence about recommendations. The unit tests all passed. The bug was found only by running the thing against reality and reading the five things it said were wrong.

## Implications

1. **Self-tests are necessary but insufficient.** A harness that only tests itself has caught nothing.
2. **Live runs are the real audit.** Budget for them explicitly.
3. **False positives are a feature, not noise.** They reveal where your metrics are sloppy.
4. **Store raw responses.** You cannot debug a metric without the original inputs.

---

## See Also

- [manual/20-evaluation/benchmark-methodology.md](../20-evaluation/benchmark-methodology.md) — How to structure evaluation frameworks
- [manual/50-principles/decision-logging.md](../50-principles/decision-logging.md) — The ADL system that logged this decision
- [ER-002: Eval Metric Calibration](../70-engineering-reviews/ER-002-eval-metric-calibration.md) — Retrospective on the broader calibration problem
