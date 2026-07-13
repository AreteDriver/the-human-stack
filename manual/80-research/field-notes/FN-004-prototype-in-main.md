# Field Note #004: The Prototype in Main Anti-Pattern

## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** Third experiment using "prototype in main" pattern
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Observation

The most dangerous scope decision is not "build a fork" or "build a branch" — it is "prototype in main." A feature starts as experimental code in the main branch, under a feature flag or in a namespace, with the intent to either promote or remove it. But once it exists in main, removal becomes politically and technically difficult. The prototype becomes permanent.

## Context

Three instances from the portfolio:

### Instance 1: Animus-Mind Fork (ER-001)

The fork was created *because* prototyping in main was rejected as too risky. The alternative — feature flags in v2.3 — was evaluated and rejected. But the fork itself became a graveyard: 2 of 10 modules complete, architecture drift, increased maintenance burden.

**What would "prototype in main" have looked like?** Experimental namespaces under `animus/experimental/` with explicit kill criteria. If modules were not complete by a deadline, the namespace would be deleted.

### Instance 2: Gorgon Self-Improvement

The self-improvement subsystem was partially implemented in Forge's main codebase. Safety infrastructure was real; code generation was stubbed. The stubs persisted for months because "we were close" and "the safety layer was valuable." In reality, the stubs masked the absence of the core capability. The subsystem was eventually replaced by the Citizen architecture.

### Instance 3: Active Inference Intent Resolver

The IntentResolver (part of the Mind fork) was never shipped. It was prototyped in the fork's `experimental/` namespace with a 3-month kill criterion. When the deadline arrived, it was correctly archived. This is the only instance where "prototype in main" (or fork equivalent) succeeded — because the kill criterion was explicit and enforced.

## The Pattern

```
"Let's just put it in main under a flag. We'll clean it up later."
    │
    ▼
Code exists in main branch
    │
    ▼
Other code depends on it (accidentally or intentionally)
    │
    ▼
"We can't remove it now — things would break"
    │
    ▼
Prototype becomes permanent infrastructure
```

## Why It Happens

1. **Lower perceived friction.** Creating a branch or fork feels like "real work." Adding a file to `experimental/` feels trivial.
2. **Social proof.** Once code is in main, it has implicit approval. Reviewers see it in context, assume it belongs.
3. **Sunk cost.** "We spent two weeks on this, we can't just delete it."
4. **Missing kill criteria.** No explicit deadline or condition for removal.

## When It Works

Prototype in main *can* work if all four conditions are met:

1. **Explicit namespace:** `experimental/`, `prototype/`, clearly demarcated
2. **Kill criterion:** "If not promoted to stable by Y date, delete"
3. **No downstream dependencies:** Other code cannot import from the experimental namespace
4. **Regular audit:** Quarterly review of experimental namespace contents

## Implication

The default should be **prototype in branch** (short-lived, max 3 months) rather than **prototype in main**. The cost of branch maintenance is lower than the cost of removing entrenched experimental code.

## Open Questions

- Can CI enforce "no imports from experimental/ by non-experimental code"?
- What is the optimal maximum lifetime for an experimental namespace? 3 months? 6 months?
- Does this pattern apply to documentation and configuration as well as code?

## References

- [ER-001: Mind-Class Fork](../../70-engineering-reviews/ER-001-mind-class-fork.md)
- [ER-002: Autonomous Code Modification](../../70-engineering-reviews/ER-002-autonomous-code-modification.md)
- [Gorgon Failure Analysis](../../60-failed-experiments/gorgon.md)

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial field note with 3 portfolio instances, pattern analysis, and conditions for success |
