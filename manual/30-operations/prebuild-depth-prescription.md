# Pre-Build Depth Prescription

## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** New archetype adoption or classification mismatch
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Problem

When an AI agent (or a human engineer) starts a project, how much should be specified before writing code? Too little and the build diverges. Too much and the specification becomes fiction — the build invalidates assumptions that were never tested.

This problem has three failure modes:

1. **Over-specification:** Spending hours on contracts and schemas for interfaces that change in the first 10 minutes of implementation. Waste.
2. **Under-specification:** Starting with a vague goal and discovering architectural contradictions 3 hours into a build. Rework.
3. **Wrong-depth mismatch:** Applying a Deep prescription to an Exploration-phase idea (premature structure) or a Shallow prescription to a Consolidation-phase system (insufficient rigor).

## Context

The portfolio contains 30+ projects spanning tools, infrastructure, games, consulting, and libraries. Each archetype has different needs at different phases. A game in Exploration needs skeleton files, not layer contracts. Infrastructure in Consolidation needs frozen interfaces, not emergent design.

The prescription must also respect real constraints:

- **Security or financial domain** → minimum Medium depth regardless of phase.
- **Tight timeline + consulting archetype** → maximum Medium depth regardless of phase.
- **Infra + stable interfaces** → default to Deep.

## Solution

Map three pre-build depths (Shallow, Medium, Deep) to project phase, timeline pressure, interface stability, and archetype. Provide explicit artifact lists, time ceilings, eval checkpoints, and 80% boundaries for each.

### Quick Reference

| Archetype | Exploration | Differentiation | Consolidation | Maturity | Decline |
|---|---|---|---|---|---|
| **tool** | Shallow | Medium | **Deep** | Medium | Medium |
| **infra** | Medium | Deep | **Deep** | Medium | Medium |
| **game** | **Shallow** | Medium | Medium | Medium | Medium |
| **consulting** | **Shallow** | Medium | Medium | Medium | Medium |

Override: `security`/`financial` guards → minimum **Medium**. `consulting` + `tight` timeline → maximum **Medium**.

### Decision Matrix

| Phase | Timeline Pressure | Interface Stability | Archetype | Prescription |
|---|---|---|---|---|
| Exploration | tight | unknown | any | **Shallow** |
| Exploration | moderate | unstable | game / consulting | **Shallow** |
| Exploration | loose | unstable | tool / infra | **Medium** |
| Differentiation | tight | unstable | any | **Medium** |
| Differentiation | moderate | stable | tool / infra | **Medium** |
| Differentiation | loose | stable | any | **Deep** |
| Consolidation | any | stable | infra / tool | **Deep** |
| Consolidation | tight | stable | game / consulting | **Medium** |
| Maturity | any | stable | any | **Medium** (maintenance scope) |
| Decline | any | any | any | **Medium** (migration scope) |

## Prescription Definitions

### Shallow

- **Artifacts:** Vision statement (1 paragraph), project scaffold (directory structure + skeleton files), single scaffold-agent `loop-md`.
- **Contracts:** None frozen. Interfaces emerge during build.
- **Eval checkpoint:** Lightweight variant (skip `evidence_quality`); composite ≥ 0.60.
- **Time ceiling:** 1 hour.

**80% Boundary (Shallow):**

| IN the 80% | DEFERRED to 20% (build-time) |
|---|---|
| Top-level directory structure | Exact file names beyond skeleton placeholders |
| Single-sentence tech stack choice | Dependency versions, migration paths |
| One-paragraph user story / goal | Full user journeys, edge cases |
| Single scaffold-agent objective | Multi-agent breakdown |

### Medium

- **Artifacts:** Vision statement, architecture notes (3–5 bullet risks/decisions), per-stream `loop-md`s (≤4), no frozen LCK contracts.
- **Contracts:** Interface sketches in comments, not formal schemas.
- **Eval checkpoint:** Full `personal-quality`; composite ≥ 0.70.
- **Time ceiling:** 2 hours.

**80% Boundary (Medium):**

| IN the 80% | DEFERRED to 20% (build-time) |
|---|---|
| Major module boundaries | Internal class hierarchies |
| Key risks (3–5 bullets) | Full risk register with mitigations |
| Per-stream objectives and stop conditions | Exact implementation algorithms |
| Interface sketches (comments / pseudo-types) | Formal schemas, serialization formats |
| Happy-path integration points | Error handling strategies, retry policies |

### Deep

- **Artifacts:** Vision statement, ADR-style architecture notes, LCK layer contracts with async DAG, per-stream `loop-md`s (≤6), synthetic verification checkpoint.
- **Contracts:** Frozen layer contracts with version hashes.
- **Eval checkpoint:** Full `personal-quality`; composite ≥ 0.70 + synthetic tests derived from spec.
- **Time ceiling:** 4 hours.

**80% Boundary (Deep):**

| IN the 80% | DEFERRED to 20% (build-time) |
|---|---|
| Frozen LCK layer contracts (interfaces) | Exact internal data structures, caches |
| Async DAG with dependency edges | Scheduling strategy (round-robin vs priority) |
| ADRs for top 3 architectural decisions | Alternatives considered, detailed trade-off matrices |
| Synthetic tests derived from spec | Full test suite, performance benchmarks |
| Per-stream objectives with safety guards | Detailed logging, observability strategy |

## Implementation

### Mid-Build Transition Rules

Pre-build depth is not prison. If build-time discovery reveals the classification was wrong:

1. **Discovery:** Agent hits a contradiction requiring scope/interface change.
2. **Flag:** Agent pauses and emits `TRANSITION_NEEDED` with reason.
3. **Human decides:**
   - **Minor tweak** (fits within current prescription's 20%) → continue, log note.
   - **Material change** → close current ADL, start new pre-build cycle.
4. **No silent upgrades:** A Shallow project cannot "accidentally" become Deep without a new ADL.

## Evidence

Applied across 30+ portfolio projects since May 2026. Classification accuracy improved after adding archetype-specific 80% boundaries. Transition rule invoked 4 times; all resulted in material changes that were correctly re-scoped.

### Quality Metrics

| Metric | Target | Actual |
|---|---|---|
| Classification mismatch (post-hoc) | <10% | 4 instances / 30+ projects |
| Time within ceiling | >80% | ~85% (Deep prescriptions occasionally exceed due to contract drafting) |
| Eval checkpoint pass rate | >90% | ~92% (fails caught before build start) |

### Limitations

- **Controlled conditions:** Single-operator environment. Not validated with multiple engineers or teams.
- **No automated enforcement:** The agent must emit `TRANSITION_NEEDED`. There is no automatic gate.
- **Archetype coverage:** Only four archetypes mapped (tool, infra, game, consulting). Library, mobile, data pipeline, and ML model archetypes are partially mapped (see [80-Percent Criteria by Archetype](eighty-percent-criteria.md)).

## Tradeoffs

- **Optimized for:** Preventing both over-specification and under-specification through explicit boundaries.
- **Sacrificed:** Flexibility. The matrix can feel rigid for hybrid projects that span archetypes.
- **Risk:** Misclassification. If the archetype or phase is wrong, the prescription pushes the project toward the wrong depth.

## Failure Modes

1. **Phantom precision:** A Shallow prescription with excessively detailed skeleton files. Mitigation: Skeleton files contain placeholders only; no implementation logic.
2. **Deferred dependency drift:** Build-time discovery reveals a dependency conflict that a Deep prescription would have caught. Mitigation: Even Shallow prescriptions list major external dependencies.
3. **Human override fatigue:** If every project gets manually overridden, the matrix loses meaning. Mitigation: Track override reasons in ADL; review monthly.

## Future Work

- **E4 target:** Measure correlation between prescription depth and build success (fewer rollbacks, shorter time-to-green) across a larger sample.
- **E4 target:** Automated archetype detection from project description (reduce classification error).
- **E5 target:** Independent reproduction by another team with different project portfolio.

## Citation

```bibtex
@misc{humanstack-ops-prebuild-2026,
  title = {Pre-Build Depth Prescription},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E3},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/30-operations/prebuild-depth-prescription.md}
}
```

## References

- [80-Percent Criteria by Archetype](eighty-percent-criteria.md)
- [Eval Checkpoint Protocol](eval-checkpoint-protocol.md)
- [ER-001: Mind-Class Fork](../70-engineering-reviews/ER-001-mind-class-fork.md) — Example of mis-classified scope leading to fork failure
- [MATURITY.md](../../MATURITY.md)

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial prescription with Shallow/Medium/Deep definitions and transition rules |
