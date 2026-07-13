# Kernel-Head-Citizen Architecture

## Evidence

**Level:** E4
**Confidence:** High
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** New subsystem design or citizen addition
**Expected Upgrade:** E5
**Owner:** @AreteDriver

---

## Problem

Agent frameworks typically conflate reasoning, execution, and governance into a single loop. This creates three problems:

1. **Brittleness:** When the reasoning layer changes, execution breaks because they share state.
2. **Opacity:** No boundary between what the agent *decided* and what it *did* — impossible to audit.
3. **Lock-in:** Architecture assumes a specific model provider, tool set, or deployment pattern.

## Context

In early 2026, Animus v2.1 was an 8-plane exocortex — powerful but monolithic. As the system grew to 30+ projects, the need for clear separation between reasoning, execution, and governance became critical. The system needed to support:

- Multiple LLM providers (local Ollama, Anthropic, OpenAI)
- Long-lived autonomous observers (citizens) without destabilizing the core
- Hardware-agnostic deployment (laptop to cloud cluster)
- Evidence-first governance (no action without proof)

## Solution

Separate the system into three layers with strict contracts:

### Layer 1: Kernel (Execution)

The kernel handles everything that must not fail: scheduling, resource management, persistence, networking, sandboxing. It is the operating system layer.

| Component | Responsibility | Key Property |
|---|---|---|
| **Budget Manager** | Token accounting and cost ceilings | Hard limit enforcement |
| **Executor** | Async DAG scheduling | Resumable via SQLite checkpoints |
| **Sandbox** | Validate changes before application | Filesystem-copy isolation (see limitations) |
| **Safety Checks** | Config validation, forbidden skill blocking | Deterministic rule evaluation |
| **Resume** | State persistence and workflow recovery | Crash-safe, no lost work |

**Key insight:** The kernel was extracted from Forge into a standalone package (`packages/kernel/`) so it can be used by other systems without importing the full agent stack.

### Layer 2: Head (Natural Language Interface)

The head is the user-facing interface that translates between human intent and kernel commands.

| Component | Responsibility | Key Property |
|---|---|---|
| **Intent Parser** | Map natural language to structured commands | Deterministic regex (20+ patterns, 4 intent types) |
| **Planner** | Dependency-aware step generation | Handles vague requests with planning hints |
| **Synthesizer** | Wrap tool results in natural language | Context-aware formatting |
| **Context Manager** | Session persistence, pruning, summarization | Preserves tool-call pairs, min 2 user turns |
| **Quality Gate** | Deterministic scoring before execution | Tool/completeness/structure dimensions |

**Key insight:** Deterministic intent parsing (regex) outperformed LLM-based routing for CLI-scale usage — faster, zero variance, sufficient for 20+ patterns. LLM routing was deferred to a future scale threshold.

### Layer 3: Citizens (Autonomous Observers)

Citizens are long-lived specialist agents that observe the system and propose improvements. They never modify code directly.

**Lifecycle:**

```
Observe → Analyze → Propose → Human Approval → Forge → Evidence → Merge
```

**Phase 0 — Mind Foundation Citizens (implemented):**

| Citizen | Role | Maturity | Evidence |
|---|---|---|---|
| **001 — Architect** | Detects technical debt, produces improvement proposals | Stage 6 (Self-improving) | E4 — produced approved proposal through full pipeline |
| **002 — Conversation Designer** | Detects repeated prompts, vague requests, correction loops | Stage 6 (Self-improving) | E4 — analyzed 534 human prompts across 38 sessions |
| **003 — Knowledge Curator** | Maintains accuracy, detects drift, harvests patterns | Stage 3 (Tested) | E3 — tests pass, no live proposal yet |
| **004 — Test Oracle** | Analyzes test suite health, coverage trends | Stage 3 (Tested) | E3 — tests pass, no live proposal yet |

**Generational roadmap:**
- **G1:** Mind Foundation (Architect, Conversation Designer, etc.) — current
- **G2:** Research Foundation (Distributed Systems, OS, AI, Databases, etc.)
- **G3:** Domain Specialists (Medicine, Finance, Manufacturing, etc.)
- **G4:** Personal Citizens (Health, Learning, Writing, Calendar, etc.)

## Design Principles

1. **Sovereignty-first:** Human owns every decision. Citizens propose; humans approve. No exceptions.
2. **Evidence before action:** Every claim needs evidence, expected benefit, risks, and success metrics.
3. **Budget-first execution:** Every agent has a token budget. Every workflow has a cost ceiling.
4. **Compounding intelligence:** Every improvement increases the rate of future improvements. Mind improves before citizens expand.
5. **Checkpoint/resume:** All workflows persist state to SQLite. Fail at step 4 of 6? Restart at step 4.
6. **Provider-agnostic:** LLM calls go through a shared interface. Swap Claude for OpenAI or Ollama without touching agent code.
7. **Local-first:** Your memory, your identity, your hardware. Nothing leaves unless configured.

## Evidence

### Implementation Verification

- **Kernel extraction:** 172 files, 55K LOC, all critical imports verified.
- **Head phases 1–5:** 72/72 head tests green, 179/179 kernel suite green.
- **Citizen tests:** 35 citizen tests + 2722 regression suite green.
- **Session continuity:** `test_five_session_continuity` — 5 sequential sessions, all messages and metadata survive via SQLite checkpoints.

### Architecture Verification

- **Truth Baseline:** 8-point validation script verifying schema, imports, and critical paths.
- **Package isolation:** Kernel can be imported without Forge or Citizens. Citizens depend on Kernel, not vice versa.
- **Provider abstraction:** Tested with Anthropic, OpenAI, and Ollama backends without code changes in upper layers.

## Tradeoffs

- **Optimized for:** Reliability, auditability, and hardware independence.
- **Sacrificed:** Simplicity. Three layers with contracts add overhead compared to a single-loop agent.
- **Risk:** Over-engineering. For simple use cases (single-session chat), the full architecture is unnecessary. The kernel supports standalone mode for these cases.

## Failure Modes

1. **Citizen proposal flood:** If multiple citizens produce proposals simultaneously, the Council may miss deduplication opportunities. Mitigation: Citizen Council with hash-based deduplication.
2. **Kernel sandbox escape:** The sandbox provides filesystem-copy isolation only — no network or process containment. Mitigation: Security-critical operations run in separate processes; see [Gorgon failure analysis](../60-failed-experiments/gorgon.md).
3. **Head context bloat:** Without pruning, session context grows unbounded. Mitigation: Automatic pruning preserves tool-call pairs and minimum 2 user turns; summary generation compacts old context.
4. **Provider drift:** Different providers interpret the same prompt differently. Mitigation: Provider-agnostic prompt templates with provider-specific post-processing.

## Open Questions

- At what citizen count does the Council become a bottleneck? (Currently 4 citizens; untested at 10+.)
- Does the deterministic intent parser scale to 50+ patterns, or does it need LLM fallback?
- What is the optimal checkpoint frequency for long-running workflows?

## Future Work

- **E4 target:** Validate architecture under multi-tenant production workloads.
- **E5 target:** Independent reproduction by third-party team building on the same principles.
- **G2 citizens:** Research Foundation layer (distributed systems, compilers, databases).

## Citation

```bibtex
@misc{humanstack-arch-kernel-head-citizen-2026,
  title = {Kernel-Head-Citizen Architecture},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E4},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/40-architecture/kernel-head-citizen.md}
}
```

## References

- [CS-001: Animus P5 Discovery](../10-case-studies/CS-001-animus-p5-discovery.md)
- [CS-004: Session Controller](../10-case-studies/CS-004-session-controller.md)
- [ER-001: Mind-Class Fork](../70-engineering-reviews/ER-001-mind-class-fork.md)
- [Gorgon Failure Analysis](../60-failed-experiments/gorgon.md) — Sandbox limitations
- Animus Architecture Overview: `animus/docs/architecture/overview.md`
- Animus v2.3 Mind Foundation ADL: `decisions/2026-07.md` (ADL-20260705-001)

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial architecture documentation with three-layer separation, citizen lifecycle, and design principles |
