# Maturity Model

This document tracks the maturity of ideas and capabilities described in The Human Stack. It helps readers understand what to trust and what to treat as experimental.

## Maturity Levels

| Level | Name | Meaning |
|-------|------|---------|
| **M0** | Concept | An idea with no implementation. |
| **M1** | Experimental | Implemented, but unstable. API or design may change significantly. |
| **M2** | Developing | Core design is stabilizing. Used in limited contexts. |
| **M3** | Stable | Proven in multiple contexts. Breaking changes are rare. |
| **M4** | Production | Used in production workloads. Operational runbooks exist. |
| **M5** | Established | Widely adopted. Industry best practice. |

## Relationship to Evidence Framework

Maturity and evidence are related but distinct:

- **Maturity** describes the stability of an idea or capability.
- **Evidence** describes the confidence in the claims made about it.

A mature concept (M4) can have low-confidence evidence (E2) if it was never rigorously benchmarked. A new concept (M1) can have high-confidence evidence (E3) if it was thoroughly evaluated in controlled conditions.

## Capability Registry

| Capability | Maturity | Evidence | Notes |
|-----------|----------|----------|-------|
| Deployment Playbook | M2 | E2 | Drafted, not yet validated in enterprise contexts |
| Evaluation Framework | M3 | E3 | Benchmarked across 4 models, 3 suites |
| drift-monitor | M3 | E3 | Detects regressions; production validated in limited scope |
| Animus P5 Discovery | M2 | E3 | 41 tests; benchmarked but limited production exposure |
| Policy Decision Point | M3 | E3 | 16 adversarial tests; deterministic evaluation |
| Active Inference (Intent Resolution) | M1 | E1 | Experimental; not yet benchmarked |
| memboot | M3 | E3 | Context bootstrapping; validated in test suite |

## How to Update

When a capability matures or new evidence emerges, update this table via pull request with:
- Capability name
- New maturity level and rationale
- New evidence level and rationale
- Links to supporting chapters or benchmarks
