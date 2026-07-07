# Engineering Review #001: Mind-Class Fork

## Evidence

**Level:** E4
**Confidence:** High
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-07
**Review Trigger:** Third experiment using "prototype in main" pattern
**Expected Upgrade:** E5
**Owner:** @AreteDriver

---

## Original Goal

Create a Mind-class fork of Animus that would serve as a separate, more opinionated implementation of the sovereign AI operating environment concept. The fork was intended to explore alternative architectures for memory, policy, and governance without constraining the mainline v2.3 development.

## Context

In early 2026, the Animus v2.3 architecture was stabilizing around the kernel-head-citizen model. Simultaneously, several experimental ideas were emerging that did not fit cleanly into the existing roadmap:

- Bitemporal memory with valid-time and transaction-time axes
- Policy Decision Point with capability-based access control
- DurableObjectStore for long-lived state
- Active inference for intent resolution

Rather than disrupt v2.3, a fork was created to explore these ideas independently.

## Decision

**Chosen:** Create a separate `animus-mind` fork with its own versioning and release cycle.

**Alternatives considered:**

- **A:** Integrate experimental features directly into v2.3 under feature flags.
  - *Rejected:* Risked destabilizing the core kernel-head architecture.
- **B:** Create a long-lived branch within the main repository.
  - *Rejected:* Branch divergence would complicate CI and release management.
- **C:** Archive the ideas entirely and revisit later.
  - *Rejected:* The ideas had merit; postponing would lose momentum.

## Evidence Gathered

Over 6 months of development:

- **Modules implemented:** 2 of 10 planned (DurableObjectStore, PolicyDecisionPoint)
- **Test coverage:** Bitemporal core + adversarial tests passed
- **Fragmentation observed:** Architecture drift between mind and v2.3 increased maintenance burden
- **Integration complexity:** Mind-class concepts required significant refactoring to upstream

## Unexpected Outcomes

1. **The fork proved concepts but created divergence.** DurableObjectStore and PolicyDecisionPoint worked, but their designs diverged from v2.3 conventions.

2. **Bitemporal memory was overkill for immediate needs.** While theoretically sound, no production workload required valid-time queries. Transaction-time alone was sufficient.

3. **Active inference was premature.** The IntentResolver concept was intriguing but lacked sufficient production data to validate the approach. It was never shipped.

4. **The fork became a graveyard for half-implemented ideas.** With only 2 of 10 modules complete, the repository represented ambition without follow-through.

## What Failed

- **Scope control.** The fork attempted too many experimental ideas simultaneously.
- **Integration planning.** Upstreaming successful concepts required redesign, not direct porting.
- **Resource allocation.** Development attention split between fork maintenance and v2.3 advancement.

## What Changed

Six months later, the recommendation reversed:

- **DurableObjectStore** and **PolicyDecisionPoint** were upstreamed to v2.3 (see related case studies).
- **Bitemporal memory** was deprioritized; transaction-time logging was retained.
- **Active inference** was archived as a research note (M1, E1) pending further evidence.
- **The fork itself** was archived.

## Current Recommendation

**Do not maintain long-lived forks for architectural exploration.** Instead:

1. **Prototype within the main repository** under experimental namespaces.
2. **Set explicit kill criteria** before starting: "If X modules are not complete by Y date, archive."
3. **Upstream or archive within 3 months.** No concept should linger in limbo.

## Open Questions

- Would a 3-month time limit have been too aggressive for the DurableObjectStore upstreaming? (Actual time was ~6 months.)
- Are there conditions where long-lived forks are actually preferable (e.g., security isolation, regulatory separation)?
- How should organizations with multiple teams decide between "prototype in main" vs. "fork and prove"?

## Evidence Level

This recommendation is based on direct experience with the fork and its subsequent upstreaming. The pattern of "fork → prove → upstream or archive" has been applied to two subsequent experiments with better results.

## Citation

```bibtex
@misc{humanstack-er001-2026,
  title = {Engineering Review #001: Mind-Class Fork},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E4},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/70-engineering-reviews/ER-001-mind-class-fork.md}
}
```

## References

- [CS-002: Upstreaming DurableObjectStore](../10-case-studies/CS-002-durable-object-store.md)
- [CS-003: Upstreaming PolicyDecisionPoint](../10-case-studies/CS-003-policy-decision-point.md)
- [80-research: Active Inference](../80-research/active-inference.md)

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-07 | Initial review |
