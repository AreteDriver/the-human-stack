# 80-Percent Criteria by Archetype

## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** New archetype adoption
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Problem

Scope creep disguised as "being thorough." Pre-build artifacts balloon with implementation details that should emerge during build. The result: rigid specifications that the first 30 minutes of coding invalidate, or specifications so vague they provide no guardrails.

The 80/20 boundary is a design decision, not a natural law. It must be explicit per archetype.

## Solution

For each archetype, define what belongs in the 80% pre-build artifact set versus what is intentionally deferred to build-time discovery. Apply the sanity check: when in doubt, **defer**.

## Prescription by Archetype

### Tool (e.g., CLI utility, MCP server, Forge plugin)

**80%:** Core user-visible features, public API or CLI structure, auth model (type, not implementation), data persistence strategy, 3–5 top-level error classes, major external dependencies.

**20%:** Internal module boundaries beyond top-level, specific algorithms, logging levels, config schema details, edge cases beyond top 3, admin/monitoring endpoints, packaging scripts.

### Infrastructure (e.g., service mesh, daemon, monitor)

**80%:** Service topology diagram, protocol/transport layer, data contracts between services, failure mode assumptions, observability hook points, deployment unit.

**20%:** Retry/backoff policies, circuit breaker thresholds, DB indexing, caching specifics, scaling triggers, alerting thresholds, IaC parameter values.

### Game (e.g., card game, fantasy sports, strategy)

**80%:** Core loop definition (what does a player do in 60 seconds?), win/loss conditions, 3–5 core mechanics, entity types with attributes (not formulas), progression layer overview, monetization model category.

**20%:** Exact balance numbers, economy sinks/faucets calibration, advanced mechanics, matchmaking algorithm, anti-cheat, localization, platform-specific optimizations.

### Consulting / Engagement (e.g., rapid assessment, audit)

**80%:** Scope boundary (in/out), deliverable format and length, data access level, stakeholder interview list (roles, not names), risk framework categories, timeline and checkpoint milestones.

**20%:** Specific findings, client implementation plans, long-term roadmap, tooling recommendations, pricing, follow-up scoping.

### Library (e.g., npm package, Python wheel, Rust crate)

**80%:** Public API surface, semver strategy, core abstraction, dependency footprint, target platform/runtime constraints, error/exception contract, documentation structure.

**20%:** Internal directory layout, exact algorithm implementations, build toolchain config, test coverage targets, changelog automation, performance benchmarking methodology.

### Mobile App

**80%:** Platform targets and minimum OS versions, core screen/navigation map (5–7 primary flows), offline strategy category, auth and data sync model, native bridge points (existence, not code), primary user personas, monetization model category.

**20%:** Navigation transitions and animation curves, deep linking specifics, platform-specific UI adaptations, background task scheduling, push notification payload schema, local database schema and migration, accessibility audit details, beta distribution.

### Data Pipeline

**80%:** Source systems and ingestion method, transform intent, sink targets and delivery guarantee, orchestration model, schema contract and versioning policy, data quality gates (rules, not thresholds), failure recovery strategy.

**20%:** Exact cron schedule, partitioning strategy, column-level transformation formulas, join order optimization, connection pooling and batch sizes, monitoring dashboards, infrastructure provisioning scripts.

### ML Model

**80%:** Problem type and success metric, feature categories and provenance, training vs inference boundary, data validation rules, model serving interface, bias/fairness guardrails, fallback strategy.

**20%:** Exact architecture and hyperparameters, tuning grid, feature engineering formulas, cross-validation fold strategy, model registry versioning, A/B test logic, inference scaling, retraining automation triggers.

## Generic Sanity Check

Before finalizing any pre-build artifact, ask:

1. Would an engineer with domain knowledge but zero project context know where to start? → **80%**
2. Does this document commit to a specific library version or config value? → likely **20%**
3. Is this a user-facing promise or an internal convenience? → user-facing = **80%**, internal = **20%**
4. If this assumption turns out wrong, does the whole plan collapse? → **80%**
5. If this assumption turns out wrong, do we just swap a library or tweak a number? → **20%**

Apply the rule: **when in doubt, defer**. You can always deepen during build. You cannot undo over-specification without waste.

## Evidence

Derived from 30+ portfolio projects across all archetypes above. The boundary was initially implicit; explicit articulation reduced pre-build artifact bloat by approximately 40% (measured by word count and file count) while maintaining the same build success rate.

### Known Limitations

- **Single-operator bias:** Boundaries reflect one engineer's judgment. Multi-team environments may need different 80/20 splits.
- **No automated enforcement:** The criteria are advisory. An agent or human can override, but the override should be logged.
- **Library and ML archetypes:** Less validated than tool, infra, game, and consulting. More projects needed.

## Tradeoffs

- **Optimized for:** Reducing pre-build waste and preventing specification fiction.
- **Sacrificed:** Upfront completeness. Some interfaces are genuinely ambiguous until implementation begins.
- **Risk:** Excessive deference. If a critical dependency is deferred and later proves incompatible, the cost of rework exceeds the cost of early specification.

## Failure Modes

1. **Wrong-archetype application:** Applying tool boundaries to a data pipeline. Mitigation: Review archetype selection in ADL.
2. **Over-deference in security domains:** A "defer" on auth implementation can become a late-breaking vulnerability. Mitigation: Security and financial domains have a minimum Medium depth regardless of phase.
3. **Drift over time:** As the team gains experience, what was 80% may shrink to 60%. Review quarterly.

## Future Work

- **E4 target:** Automated archetype detection from project description + file tree.
- **E4 target:** Calibrate 80/20 boundaries against actual build-time discovery frequency (what percentage of "deferred" items required mid-build specification).
- **E5 target:** Cross-team validation with independent portfolios.

## Citation

```bibtex
@misc{humanstack-ops-80pct-2026,
  title = {80-Percent Criteria by Archetype},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E3},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/30-operations/eighty-percent-criteria.md}
}
```

## References

- [Pre-Build Depth Prescription](prebuild-depth-prescription.md)
- [Eval Checkpoint Protocol](eval-checkpoint-protocol.md)
- [CS-001: Animus P5 Discovery](../10-case-studies/CS-001-animus-p5-discovery.md) — Tool archetype example

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial archetype boundaries covering tool, infra, game, consulting, library, mobile, data pipeline, ML |
