# Evidence Framework

This document defines how The Human Stack grades claims. It is applied to every artifact: laws, corollaries, chapters, case studies, benchmarks, patterns, and reviews.

## Core Principle

**Evidence, confidence, and scope are three separate axes.**

| Axis | Question |
|------|----------|
| **Evidence** | How much empirical support exists? |
| **Confidence** | How strongly do we currently believe it? |
| **Scope** | How broadly do we think it applies? |

Separating these prevents the common failure modes of:
- Conflating "we tested this" with "we are certain this is correct"
- Conflating "we believe this strongly" with "this applies everywhere"
- Hiding uncertainty by upgrading evidence levels to match intuition

---

## Evidence Levels (E0–E5)

| Level | Name | Meaning |
|-------|------|---------|
| **E0** | Question | No recommendation. Pure speculation. |
| **E1** | Hypothesis | Reasoned design or observation. Plausible, but untested or limited evidence. |
| **E2** | Prototype | Demonstrated in controlled conditions. Works on the author's machine. |
| **E3** | Benchmarked | Measured. Repeatable under defined conditions. |
| **E4** | Production | Observed under real workloads. Not synthetic. |
| **E5** | Independent | Externally reproduced by someone without the author's involvement. |

### Examples

| Claim | Evidence | Explanation |
|-------|----------|-------------|
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

## Scope

| Level | Meaning |
|-------|---------|
| **Narrow** | Applies to a specific system, team, or context. |
| **Moderate** | Applies to a class of systems or organizations with similar constraints. |
| **Broad** | Applies to AI systems generally, with stated boundary conditions. |

### Example

```
Law 1: "Every AI system that creates sustained value must eventually be
        operated as an operational system."
Evidence: E1
Confidence: High
Scope: Broad
→ "We have strong engineering intuition from operational systems work,
   but have not yet systematically measured this across dozens of AI deployments.
   The scope is broad, but the evidence is thin."
```

---

## Chapter Evidence Block

Every chapter, law, and artifact in the manual must include this block at the top:

```markdown
## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Moderate
**Status:** Stable
**Version:** 1.2
**Last Review:** 2026-07-07
**Review Trigger:** Independent reproduction
**Expected Upgrade:** E4
**Owner:** @AreteDriver
```

### Field Definitions

| Field | Meaning |
|-------|---------|
| **Level** | Current evidence level (E0–E5) |
| **Confidence** | Current confidence (Low / Medium / High) |
| **Scope** | How broadly this applies (Narrow / Moderate / Broad) |
| **Status** | Draft / Stable / Archived / Superseded |
| **Version** | Manual version of this artifact |
| **Last Review** | Date of last evidence review |
| **Review Trigger** | What event would trigger a re-review? |
| **Expected Upgrade** | What evidence would justify a higher level? |
| **Owner** | Person responsible for accuracy |

### Status Definitions

| Status | Meaning |
|--------|---------|
| **Draft** | Initial version. Under review. Subject to significant change. |
| **Stable** | Reviewed. Evidence verified. Minor corrections expected. |
| **Archived** | Retained for historical context. No longer maintained. |
| **Superseded** | Replaced by a newer artifact. Link to successor. |

### Re-review Requirements

| Evidence Level | Re-review Frequency |
|----------------|---------------------|
| E0–E1 | Every 3 months |
| E2–E3 | Every 6 months |
| E4–E5 | Every 12 months |

If evidence degrades (e.g., E3 findings are contradicted by new data), the artifact must be updated or downgraded. **Public downgrades are a feature, not a bug.**

---

## Upgrading and Downgrading Evidence

Evidence levels do not increase automatically. They require:

- **E1 → E2:** Working prototype demonstrated.
- **E2 → E3:** Benchmark report with reproducible methodology.
- **E3 → E4:** Production deployment with observed metrics.
- **E4 → E5:** External reproduction documented by at least one independent party.

Downgrades happen when:
- New data contradicts prior evidence
- Conditions change, invalidating prior measurements
- Scope is discovered to be narrower than originally claimed

Each change must be recorded in the artifact's version history with rationale.

---

## Evidence Levels by Artifact Type

| Artifact | Typical Starting Level | Typical Target Level |
|----------|----------------------|---------------------|
| Law | E1 | E4 |
| Corollary | E2 | E4 |
| Pattern | E2 | E3 |
| Runbook | E3 | E4 |
| Benchmark | E3 | E4 |
| Engineering Review | E3 | E4 |
| Case Study | E3 | E4 |
| Hypothesis | E0 | E2 |
| Field Note | E1 | E2 |

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
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-07
**Review Trigger:** Multi-tenant production validation
**Expected Upgrade:** E4
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

| Attribute | Level | Confidence | Scope |
|-----------|-------|------------|-------|
| This framework | E3 | Medium | Moderate |
| Rationale | Used across all artifacts in this manual. Not yet independently reproduced. Under active refinement. | | |

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.1 | 2026-07-07 | Added Scope axis, Review Trigger, Expected Upgrade, downgrade rules, artifact-type table |
| 1.0 | 2026-07-07 | Initial framework |
