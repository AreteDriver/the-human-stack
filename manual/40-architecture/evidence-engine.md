# Evidence Engine Design

## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Narrow
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** New subsystem claiming maturity Stage 3+
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Problem

AI systems accumulate claims faster than evidence. A subsystem is "documented" because a README exists. A feature is "tested" because `pytest` passes. A model is "validated" because a benchmark was run once. These are not the same thing.

Without explicit evidence tracking, three failures occur:

1. **Promotion fiction:** Subsystems advance through maturity stages without the evidence to justify it.
2. **Regression blindness:** A passing test suite hides degrading evidence quality or coverage gaps.
3. **Audit impossibility:** No trail connects a claim to the specific test, benchmark, or observation that supports it.

## Context

In July 2026, Animus had 9 G1 (Mind Foundation) subsystems at varying maturity stages. Some had live production evidence. Some had tests only. Some had design docs and little else. The need for a unified evidence framework became acute when closing Kill Criterion #4 (Evidence Coverage Baseline).

The Evidence Engine must:
- Define what "evidence" means for each maturity stage
- Track coverage (what percentage of claims have matching evidence)
- Provide audit trails (who verified, when, how)
- Gate promotion (no stage advancement without evidence)

## Solution

### Feature Maturity Stages

| Stage | Name | Criteria | Evidence Required |
|---|---|---|---|
| 0 | **Concept** | Design doc exists | Document exists, dated |
| 1 | **Scaffolded** | Code structure in place | Directory tree + skeleton files |
| 2 | **Implemented** | Logic complete, compiles/runs | Build passes, basic smoke test |
| 3 | **Tested** | Unit/integration tests pass | Test suite green, coverage >80% |
| 4 | **Validated** | Evaluated against rubric | Eval run with score, rubric version, conditions |
| 5 | **Verified** | Adversarial review passed | Independent reviewer sign-off, identified failure modes |
| 6 | **Self-improving** | Citizens actively improve it | Live proposals produced and commissioned through full pipeline |

**Key rule:** Stage N+1 requires *all* evidence for Stage N, plus new evidence specific to Stage N+1. Evidence does not replace prior evidence; it accumulates.

### Evidence Coverage KPI

```
Coverage = (Σ subsystem_coverage_scores) / (number of subsystems × 100)
```

Where each subsystem's coverage is:
- 100% if all claims at its current stage have matching evidence
- Proportional if some claims are unsubstantiated

**Target:** ≥80% coverage with zero Stage 0–1 subsystems before any promotion.

### Evidence Manifest Format

Every subsystem at Stage 3+ maintains a manifest:

```yaml
subsystem: Architect Citizen (001)
stage: 6
claims:
  - claim: Observes codebase and produces ImprovementProposal
    evidence:
      - type: unit_test
        source: packages/core/tests/test_citizens.py (18 tests)
        status: PASS
      - type: live_usage
        source: Proposal ADL-20260706-19b268
        status: VERIFIED
    coverage: 100%
  - claim: Analyzes dependencies and detects god classes
    evidence:
      - type: senior_analysis_test
        source: dependency_analysis, god_class_detection
        status: PASS
    coverage: 100%
```

### Integration Points

- **Forge approval gates:** Subsystems without evidence manifests cannot be promoted.
- **ADL entries:** Every architectural decision includes expected evidence target.
- **Truth Baseline evaluation:** 8-point validation script verifies schema, imports, critical paths — a quick check, not a substitute for full evidence.

## Evidence

### G1 Evidence Coverage Baseline (2026-07-06)

Applied to all 9 Mind Foundation subsystems:

| Subsystem | Stage | Evidence Coverage | Blocking Gaps |
|---|---|---|---|
| Architect (001) | 6 | 100% | None |
| Conversation Designer (002) | 6 | 100% | None |
| Knowledge Curator (003) | 3 | 60% | Needs live proposal |
| Test Oracle (004) | 3 | 60% | Needs live proposal |
| Citizen Council | 3 | 60% | Needs multi-citizen test |
| Proposal Queue | 5 | 100% | None |
| Forge Commissioner | 5 | 100% | None |
| BootstrapLoop | 4 | 80% | Needs stress test |
| Eval Evidence | 4 | 70% | Needs citizen consumer |

**Aggregate KPI: 81.1%** (target ≥80% satisfied).

### Kill Criteria Closed Using Framework

| KC | Subsystem | Evidence Required | Status |
|---|---|---|---|
| #1 | Architect | ≥1 approved proposal through full pipeline | ✅ Closed |
| #2 | Conversation Designer | Detect patterns + propose measurable improvement | ✅ Closed |
| #4 | Evidence Coverage | ≥80% coverage, all ≥ Stage 3 | ✅ Closed |
| #5 | Continuity | ≥5 sessions with checkpoint persistence | ✅ Closed |

## Tradeoffs

- **Optimized for:** Honest assessment of subsystem maturity.
- **Sacrificed:** Speed. Maintaining evidence manifests adds overhead to every change.
- **Risk:** Gaming. A subsystem can optimize for coverage score rather than real evidence quality. Mitigation: Adversarial review at Stage 5.

## Failure Modes

1. **Coverage inflation:** Marking evidence as "present" when it is weak or stale. Mitigation: Evidence entries include timestamps; stale evidence (>90 days) flags for review.
2. **Stage creep:** Advancing a subsystem because adjacent subsystems advanced, not because its own evidence improved. Mitigation: Per-subsystem assessment, no transitive promotion.
3. **Manifest rot:** Manifests not updated as subsystems evolve. Mitigation: Citizen Council review includes manifest freshness check.

## Future Work

- **E4 target:** Automate manifest generation from test suites, eval runs, and ADL entries.
- **E4 target:** Correlate evidence coverage with production incident rate.
- **E5 target:** Independent reproduction — another team applies framework to different codebase and achieves consistent results.

## Citation

```bibtex
@misc{humanstack-arch-evidence-2026,
  title = {Evidence Engine Design},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E3},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/40-architecture/evidence-engine.md}
}
```

## References

- [Benchmark Methodology](../20-evaluation/benchmark-methodology.md)
- [CS-001: Animus P5 Discovery](../10-case-studies/CS-001-animus-p5-discovery.md)
- Animus G1 Evidence Baseline: `animus/docs/metrics/G1_EVIDENCE_BASELINE.md`
- Animus Evidence Framework memory: `notes/topics/animus-evidence-framework.md`
- ADL-20260705-001: v2.3 Mind Foundation Architecture

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial evidence framework with 7 maturity stages, coverage KPI, and manifest format |
