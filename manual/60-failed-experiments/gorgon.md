# Failed Experiment: Gorgon Multi-Agent Orchestration

## Evidence

**Level:** E4
**Confidence:** High
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** New autonomous code modification system design
**Expected Upgrade:** None (archived)
**Owner:** @AreteDriver

---

## What It Was

Gorgon was an autonomous code improvement subsystem for Animus. It detected problems in the codebase, planned fixes, implemented them in an isolated sandbox, ran tests, and created pull requests — all without human intervention.

## Original Goal

Create a self-improving agent platform: Animus observes its own code, identifies quality issues, and repairs them through an automated pipeline.

## Context

In early 2026, Animus v2.3 was stabilizing. The Forge subsystem had been designed with self-improvement as a core capability. Gorgon was the implementation attempt.

Constraints:

- Must not modify auth, security, or self-improvement infrastructure
- Must run in isolated sandbox before applying changes
- Must require human approval at plan, apply, and merge stages
- Must create snapshots for rollback

## What Was Built

A 10-stage linear orchestrator with the following components:

| Component | Status | Assessment |
|---|---|---|
| CodebaseAnalyzer | Partial | Regex-only analysis by default; AI variant existed but was never called |
| SafetyChecker | Complete | File protection, line limits, category blocking worked |
| ApprovalGate | Partial | Returned immediately; no waiting mechanism |
| Sandbox | Functional | Created temp directory copy, ran subprocesses, enforced timeout |
| RollbackManager | Functional | File-content snapshots worked, but no atomicity |
| PRManager | Functional | Real git commands, created branches and PRs |
| Orchestrator | Stubbed | Stages 4, 5, 8 (implement, test, apply) were non-functional |

## What Failed

### 1. No Code Generation Engine

Stages 4 (implementation), 5 (testing), and 8 (apply) were stubs. The system could detect problems and plan fixes, but could not modify a single line of code autonomously.

```
Stage 4: logger.info("Implementation would happen here")
Stage 5: SandboxResult(SUCCESS, tests_passed=True, lint_passed=True)  # hardcoded
Stage 8: logger.info("Would actually apply changes here")
```

### 2. Sandbox Was Not Actually Secure

The "sandbox" was a temp directory copy. It provided:

- ✅ Filesystem isolation (copy of source tree)
- ❌ No network isolation (full internet access)
- ❌ No process isolation (could see/signal other processes)
- ❌ No environment isolation (inherited API keys, tokens)
- ❌ No IPC isolation (shared memory, Unix sockets accessible)

Verdict: **Filesystem-copy isolation only.** Not safe for untrusted code generation.

### 3. Approval Gate Had No Waiting Mechanism

The approval system returned immediately with "Plan approval required." There was no polling, webhook, or notification integration. A human had to manually check and approve, but the system did not wait.

### 4. Rollback Could Not Handle New Files

The snapshot system restored known files but did not track files created during the (non-existent) implementation phase. If it had worked, new files would have persisted after rollback.

### 5. Identity File Protection Gap

The safety config protected auth and secrets, but not:
- Agent behavior files (`agents/supervisor.py`)
- Workflow definitions (`workflows/**`)
- CLI entry points (`cli/**`)
- Persona/identity configuration

A "refactoring" could have modified agent behavior without triggering safety checks.

### 6. Outer Try/Except Masked Bugs

All errors caught, logged, and swallowed. Silent failures were the default.

## What We Learned

1. **Safety infrastructure can outpace the capability it guards.** Gorgon had excellent safety design (file protection, category blocking, approval gates, rollback) but nothing worth guarding.

2. **Filesystem-copy isolation is not a sandbox.** For code generation from an AI model, network and process isolation are essential. The threat model must assume generated code is adversarial until proven otherwise.

3. **Approval without waiting is not approval.** A gate that returns immediately is a speed bump, not a checkpoint.

4. **Self-improvement is harder than it appears.** Detecting code issues (static analysis) is tractable. Generating correct fixes (code synthesis) requires a level of capability that did not exist in the system at the time.

## What Replaced It

Gorgon was replaced by the **Animus Forge Commissioner** + **Citizen Council** architecture:

- **Citizens** (Architect, Conversation Designer, Knowledge Curator, Test Oracle) observe and propose improvements.
- **Citizen Council** ranks and deduplicates proposals.
- **Forge Commissioner** bridges approved proposals to Forge workflows.
- **Human approval** is required at the proposal level, not at every code change.
- **Code changes** are made by the human operator using Claude Code or IDE, not by an autonomous agent.

This shifts the autonomy boundary: the system identifies what should change, but a human decides how and executes.

## Current Recommendation

**Do not build autonomous code modification systems until the generation layer is proven.** Instead:

1. Build observation and proposal systems first (citizens).
2. Route proposals through human approval.
3. Let humans execute changes with their existing tools.
4. Only after generation quality reaches a high bar (>95% correctness on controlled tasks) consider automating application.

## Evidence Level

This recommendation is based on direct implementation experience with Gorgon and its successor systems. The pattern of "observe → propose → human execute" has been applied to 5+ approved proposals with successful outcomes.

## Open Questions

- At what generation accuracy threshold does autonomous application become viable?
- What sandbox technology (Docker, gVisor, firejail) is appropriate for AI-generated code?
- Should approval gates be synchronous (block and wait) or asynchronous (queue and notify)?

## Citation

```bibtex
@misc{humanstack-fail-gorgon-2026,
  title = {Failed Experiment: Gorgon Multi-Agent Orchestration},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E4},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/60-failed-experiments/gorgon.md}
}
```

## References

- [CS-001: Animus P5 Discovery](../10-case-studies/CS-001-animus-p5-discovery.md)
- [ER-001: Mind-Class Fork](../70-engineering-reviews/ER-001-mind-class-fork.md)
- [Animus Architecture Book v3.0](https://github.com/AreteDriver/notes/topics/animus-architecture-book/)
- Animus self-improve deep dive: `animus/docs/reviews/2026-02-21-self-improve-deep-dive.md`

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial failure analysis based on 2026-02-21 deep review and successor system validation |
