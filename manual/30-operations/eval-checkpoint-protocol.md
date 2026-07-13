# Eval Checkpoint Protocol

## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Narrow
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** Rubric or suite revision
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Problem

Pre-build artifacts (vision, classification, prescription, loop-mds, contracts) look complete but contain subtle flaws: vague stop conditions, hallucinated assumptions, premature completeness. If the build proceeds on flawed foundations, the cost of correction compounds.

The eval checkpoint is a gate, not a formality.

## Solution

Validate pre-build artifacts before authorizing execution. Use a lightweight or full eval depending on prescription depth, with explicit thresholds, retry limits, and escalation paths.

## Procedure

### 1. Select Variant

- **Shallow prescription:** run lightweight checkpoint (Step 2a).
- **Medium / Deep prescription:** run full checkpoint (Step 2b).

### 2a. Lightweight Checkpoint (Shallow)

- Use `personal-quality` rubric with `evidence_quality` dimension removed.
- Remaining dimensions: `relevance`, `precision`, `actionability`, `format_compliance`, `hallucination_safety`.
- **Pass threshold:** composite ≥ 0.60.
- **Action:** If pass → proceed to build. If fail → agent regenerates vision/scaffold, max 2 retries.

### 2b. Full Checkpoint (Medium / Deep)

- Use `personal-quality` rubric unchanged.
- **Pass threshold:** composite ≥ 0.70.
- **Action:** If pass → freeze artifacts, proceed to build. If fail → agent revises, max 2 retries.

### 3. Log Result

Append eval score and rubric version to the ADL entry:

```yaml
metadata:
  eval_checkpoint:
    rubric: personal-quality | personal-quality-light
    composite_score: 0.78
    pass_threshold: 0.70
    passed: true
    timestamp: "2026-06-21T15:00:00Z"
```

## Score Interpretation

| Composite | Meaning | Action |
|---|---|---|
| ≥ 0.90 | Exceptional | Freeze and build. Check if 80% cap was violated (too much detail?). |
| 0.80 – 0.89 | Strong | Freeze and build. Minor gaps acceptable within 20% deferred. |
| 0.70 – 0.79 | Acceptable (Deep/Medium pass) | Freeze and build. Flag lowest-scoring dimension. |
| 0.60 – 0.69 | Marginal (Shallow pass only) | For Shallow: freeze. For Medium/Deep: retry. |
| 0.50 – 0.59 | Below threshold | Retry once. Focus on lowest dimension. |
| < 0.50 | Poor | Do not proceed. Pivot to Exploration or human review. |

**Per-dimension red flags:**

- `precision` < 0.5 → stop_conditions are vague or implementation-leaked. Rewrite loop-mds.
- `actionability` < 0.5 → agent cannot execute from these docs. Add concrete steps.
- `hallucination_safety` < 0.5 → classification or assumptions are fabricated. Back to scope research.

## Quick-Verify Checklist (Manual Spot-Check)

Before running formal eval, answer in ≤2 minutes:

- [ ] `vision.md` is readable in one sitting (≤300 words).
- [ ] `classification.yaml` phase matches gut feeling of the project.
- [ ] Every `*.loop.md` has a `stop_condition` verifiable without reading code.
- [ ] Every `*.loop.md` with `safety_guards != ["none"]` has a human-review gate.
- [ ] `deferred_items` exists on Medium/Deep loops and names real gaps, not filler.
- [ ] Total loop-md count ≤ 8 (or ADL addendum justifies >8).
- [ ] If Deep: `contracts/*.yaml` have no `"TODO"` or `"FIXME"` in interface definitions.

If ≥6 boxes checked, formal eval likely passes. If <4, don't bother — fix first.

## Failure Path

If 2 retries fail:

1. Escalate to human review.
2. Human can override (add ADL addendum: "pre-build eval waived, reason: X") or pivot to deeper Exploration.

## Common Failure Patterns

| Pattern | Symptom | Fix |
|---|---|---|
| **Premature completeness** | Composite high but build feels rigid | Add `deferred_items`; verify 80% boundary. |
| **Vague stop conditions** | `actionability` or `precision` low | Rewrite every stop_condition as testable assertion. |
| **Over-scoped loop** | Single loop-md tries to do too much | Split. Budget > 20 is a smell. |
| **Phantom dependencies** | `dependencies` list references files that don't exist | Pre-flight dependency validation before eval. |
| **Classification mismatch** | `hallucination_safety` low | Re-run classification with human input. |

## Evidence

Protocol applied to 30+ pre-build cycles since May 2026. Pass rate ~92%. Failures caught at checkpoint prevented estimated 3–6 hours of rework per instance (based on historical rate of mid-build scope pivots before protocol adoption).

### Limitations

- **Self-judged:** The same agent system runs the eval and the build. Independent judge not yet integrated.
- **Rubric calibration drift:** Scores may drift as the judge model updates. Weekly calibration recommended.
- **No external validation:** The protocol has not been reproduced by another team.

## Tradeoffs

- **Optimized for:** Catching specification flaws before they compound.
- **Sacrificed:** Speed. A full checkpoint adds 10–20 minutes to pre-build.
- **Risk:** False security. A passing eval does not guarantee build success — it guarantees the specification is internally consistent.

## Failure Modes

1. **Checkpoint gaming:** Agent optimizes for rubric score rather than build quality. Mitigation: Periodic adversarial review of "passed" artifacts.
2. **Threshold inflation:** Raising thresholds without evidence that higher thresholds reduce failure. Mitigation: Track correlation between checkpoint score and build rollback rate.
3. **Rubric staleness:** Dimensions that matter shift as the project matures. Mitigation: Review rubric relevance quarterly.

## Future Work

- **E4 target:** Independent judge (different model or human) for 20% of checkpoints.
- **E4 target:** Automated correlation tracking between checkpoint scores and build success metrics.
- **E5 target:** External reproduction by another team with different agent system.

## Citation

```bibtex
@misc{humanstack-ops-eval-2026,
  title = {Eval Checkpoint Protocol},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E3},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/30-operations/eval-checkpoint-protocol.md}
}
```

## References

- [Pre-Build Depth Prescription](prebuild-depth-prescription.md)
- [80-Percent Criteria by Archetype](eighty-percent-criteria.md)
- [Benchmark Methodology](../20-evaluation/benchmark-methodology.md)

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial protocol with lightweight and full checkpoint variants, score interpretation, and failure path |
