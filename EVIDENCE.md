# Evidence Framework

This document defines how The Human Stack grades claims. It is applied to every chapter, case study, benchmark, and engineering review.

## Core Principle

**Evidence and confidence are separate axes.**

Something can be:

- **High evidence, low confidence** — We measured it extensively, but conflicting results exist.
- **Low evidence, high confidence** — We have strong theoretical reasons, but limited empirical validation.
- **High evidence, high confidence** — The gold standard.

Separating these prevents the common failure mode of conflating "we tested this" with "we are certain this is correct."

---

## Evidence Levels (E0–E5)

| Level | Name | Meaning |
|-------|------|---------|
| **E0** | Question | No recommendation. Pure speculation. |
| **E1** | Hypothesis | Reasoned design. Plausible, but untested. |
| **E2** | Prototype | Demonstrated in controlled conditions. Works on the author's machine. |
| **E3** | Benchmarked | Measured. Repeatable under defined conditions. |
| **E4** | Production | Observed under real workloads. Not synthetic. |
| **E5** | Independent | Externally reproduced by someone without the author's involvement. |

### Examples

| Claim | Evidence Level | Explanation |
|-------|---------------|-------------|
| "We believe active inference is the right architecture for intent resolution." | E1 | Reasoned design, no empirical validation yet. |
| "drift-monitor detects 87% of prompt regressions in our test suite." | E3 | Benchmarked, repeatable, but only under our conditions. |
| "Animus P5 Discovery reduced tool integration time from 3 days to 20 minutes across 5 production deployments." | E4 | Observed under real workloads, but not yet externally reproduced. |
| "The benchmark methodology described here has been reproduced by 3 independent teams." | E5 | Externally validated. |

---

## Confidence Levels

| Level | Meaning |
|-------|---------|
| **Low** | We would not recommend acting on this without further validation. |
| **Medium** | Reasonable to proceed, but monitor closely. Known unknowns exist. |
| **High** | We would recommend this approach under the stated constraints. |

### How Evidence and Confidence Interact

```
Evidence: E2 (Prototype demonstrated)
Confidence: High
→ "We have strong theoretical reasons and it works in controlled conditions.
   We are reasonably confident this will hold in production, but we haven't
   measured it at scale yet."
```

```
Evidence: E4 (Production validated)
Confidence: Low
→ "We've observed this under real workloads, but the results are mixed,
   conditions are narrow, or contradictory evidence exists. We cannot
   confidently generalize."
```

---

## Chapter Evidence Block

Every chapter in the manual must include this block at the top:

```markdown
## Evidence

**Level:** E3
**Confidence:** Medium
**Status:** Stable
**Version:** 1.2
**Last Review:** 2026-07-07
**Owner:** @AreteDriver
```

### Status Definitions

| Status | Meaning |
|--------|---------|
| **Draft** | Initial version. Under review. Subject to significant change. |
| **Stable** | Reviewed. Evidence verified. Minor corrections expected. |
| **Archived** | Superseded by newer chapter. Retained for historical context. |

### Re-review Requirements

| Evidence Level | Re-review Frequency |
|----------------|---------------------|
| E0–E1 | Every 3 months |
| E2–E3 | Every 6 months |
| E4–E5 | Every 12 months |

If evidence degrades (e.g., E3 findings are contradicted by new data), the chapter must be updated or downgraded.

---

## Upgrading Evidence

Evidence levels do not increase automatically. They require:

- **E1 → E2:** Working prototype demonstrated.
- **E2 → E3:** Benchmark report with reproducible methodology.
- **E3 → E4:** Production deployment with observed metrics.
- **E4 → E5:** External reproduction documented by at least one independent party.

Each upgrade must be recorded in the chapter's version history.

---

## Evidence in Practice

### Claim Without Evidence (Prohibited)

```markdown
## Recommendation

Use drift-monitor for all production deployments.
```

### Claim With Evidence (Required)

```markdown
## Evidence

**Level:** E3
**Confidence:** Medium
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-07
**Owner:** @AreteDriver

### Benchmark

- **Suite:** 500 prompts across Claude, GPT, and Ollama
- **Metric:** Regression detection rate
- **Result:** 87% detection, 4% false positive rate
- **Conditions:** Single-node deployment, synthetic load
- **Limitations:** Not validated under multi-tenant production workloads

### Confidence Rationale

Results are consistent across model families, but evaluation was limited
to synthetic workloads. Multi-tenant behavior is hypothesized (E1) but
not yet measured.
```

---

## The Evidence Framework Is Itself Evidence-Graded

| Attribute | Level |
|-----------|-------|
| This framework | E3 |
| Confidence | Medium |
| Rationale | Used across all chapters in this manual. Not yet independently reproduced. |
