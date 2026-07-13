# Engineering Review #002: Autonomous Code Modification

## Evidence

**Level:** E4
**Confidence:** High
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** New autonomous improvement system design
**Expected Upgrade:** E5
**Owner:** @AreteDriver

---

## Original Goal

Enable Animus to improve its own codebase autonomously: detect quality issues, plan fixes, implement them in isolation, validate through tests, and create pull requests.

## Context

In early 2026, the Forge subsystem included a `self_improve` module (codename: Gorgon). The architecture was a 10-stage linear orchestrator with safety checks, approval gates, sandbox isolation, and rollback capabilities. The theory was sound. The implementation was not.

## Decision

**Chosen:** Build a full autonomous modification pipeline with safety infrastructure.

**Alternatives considered:**

- **A:** Human-only improvements. *Rejected:* Would not scale with system growth.
- **B:** Semi-autonomous (detect + propose, human implements). *Partially adopted:* This became the eventual architecture via Citizens.
- **C:** Fully autonomous with no gates. *Rejected:* Unacceptable risk for a system with access to its own source code.

## Evidence Gathered

### What Worked

1. **Safety infrastructure was well-designed.** File protection (auth, secrets, self-modification blocking), category-based restrictions, quantitative limits (max 10 files, 500 lines), and multi-stage approval gates were thoughtfully implemented.

2. **Rollback system was functional.** File-content snapshots worked for their intended purpose. Creation, restoration, and cleanup operated correctly within their design constraints.

3. **PR creation was real.** Git branch creation and PR submission via `gh` CLI worked end-to-end.

### What Did Not Work

1. **No code generation engine.** Stages 4 (implementation), 5 (testing), and 8 (apply) were stubs. The system could detect problems and plan fixes, but could not modify a single line of code autonomously.

2. **Sandbox was not a sandbox.** It was a temp directory copy with zero network, process, or environment isolation. Full user privileges, full filesystem access, inherited API keys.

3. **Approval gates returned immediately.** "Plan approval required" was emitted, but the system did not wait. No polling, webhooks, or notifications.

4. **Rollback could not handle new files.** If code generation had worked, created files would persist after rollback.

5. **Identity files unprotected.** Agent behavior files, workflow definitions, and CLI entry points were not in the safety config's protected list.

6. **Silent failure by default.** Outer `try/except Exception` caught, logged, and swallowed all errors.

## Unexpected Outcomes

1. **The safety infrastructure outpaced the capability it guarded.** We built excellent guardrails around a non-existent vehicle.

2. **Filesystem-copy isolation teaches a lesson about threat modeling.** The gap between "feels isolated" and "actually isolated" was larger than expected. See [Gorgon Failure Analysis](../60-failed-experiments/gorgon.md).

3. **The approval pattern (observe → propose → human execute) proved more viable than autonomous application.** Citizens replaced the self-improve module with a proposal-driven model that has successfully closed 5 kill criteria.

## What Failed

- **Scope assumption:** We assumed code generation was the easy part and safety was hard. The opposite was true. Static analysis and safety rules are tractable. Generating correct code changes from natural language is not.
- **Integration planning:** The self-improve module had no connection to the Core Learning system. Two overlapping but incompatible approval/rollback mechanisms existed in parallel.
- **Production readiness assessment:** The system was treated as "mostly done" when 3 of 10 stages were non-functional.

## What Changed

Six months later, the architecture replaced autonomous modification with **Citizen-driven improvement:**

1. **Citizens observe and propose.** Architect, Conversation Designer, Knowledge Curator, Test Oracle — each has a specific observation domain.
2. **Citizen Council ranks and deduplicates.** No proposal flood.
3. **Forge Commissioner bridges to Forge workflows.** Approved proposals become structured tasks.
4. **Human executes changes with existing tools.** Claude Code, IDE, manual editing — not autonomous application.
5. **Evidence is required before merge.** Tests, evals, benchmarks — the same framework used for all other claims.

This shifts the autonomy boundary: the system identifies *what* should change, but a human decides *how* and executes.

## Current Recommendation

**Do not attempt autonomous code modification until the generation layer achieves >95% correctness on controlled tasks.** Instead:

1. Build observation and proposal systems first (citizens).
2. Route proposals through explicit human approval.
3. Let humans execute changes with their existing tools.
4. Only after generation quality is proven should autonomous application be reconsidered.
5. If autonomous application is attempted, use real sandboxing (Docker, gVisor, or equivalent) — not temp directory copies.

## Evidence Level

This recommendation is based on:
- Direct implementation experience with Gorgon (10-stage orchestrator, 3 stub stages)
- Successful operation of the replacement Citizen architecture (5 kill criteria closed)
- Security review of sandbox containment (14 gaps identified)

## Open Questions

- At what generation accuracy threshold does autonomous application become viable?
- What sandbox technology is appropriate for AI-generated code in a local-first environment?
- Should approval gates be synchronous (block and wait) or asynchronous (queue and notify)?
- How should organizations with different risk tolerances calibrate the autonomy boundary?

## Citation

```bibtex
@misc{humanstack-er002-2026,
  title = {Engineering Review #002: Autonomous Code Modification},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E4},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/70-engineering-reviews/ER-002-autonomous-code-modification.md}
}
```

## References

- [Gorgon Failure Analysis](../60-failed-experiments/gorgon.md) — Detailed failure modes
- [CS-001: Animus P5 Discovery](../10-case-studies/CS-001-animus-p5-discovery.md) — Citizen architecture
- [ER-001: Mind-Class Fork](../70-engineering-reviews/ER-001-mind-class-fork.md) — Related scope control failure
- Animus self-improve deep dive: `animus/docs/reviews/2026-02-21-self-improve-deep-dive.md`

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial review based on 6 months of implementation and replacement experience |
