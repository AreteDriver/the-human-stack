# Case Study #004: Session Controller

## Evidence

**Level:** E4
**Confidence:** High
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** Session crash or context bloat incident
**Expected Upgrade:** E5
**Owner:** @AreteDriver

---

## Problem

Long-running AI sessions face two failure modes:

1. **Context bloat:** As conversation history grows, the model's context window fills. Older messages vanish silently. The agent loses information without knowing it lost it.
2. **Session staleness:** Sessions left open for hours accumulate irrelevant context. A session started for debugging code at 9am is polluted by lunch conversation at 1pm. Performance degrades, but there is no clean reset mechanism.

In the Animus Head (natural language interface), both problems manifested regularly:
- Ollama sessions crashed at 100% context utilization
- Sessions left open overnight produced degraded responses
- Users manually restarted sessions, losing conversation history

## Context

Animus Head Phase 3 had implemented context management (pruning, summarization, checkpoint persistence) but treated it as a reactive cleanup, not a proactive lifecycle policy. The system could recover from context limits, but it could not prevent them.

Constraints:
- Must preserve conversation history across restarts
- Must not interrupt user mid-task
- Must support per-model overrides (different context sizes)
- Must integrate with existing checkpoint system (SQLite)
- Must be configurable via CLI, daemon RPC, and environment variables

## Solution

Build a `SessionController` that wraps sessions at configurable thresholds, gracefully finalizes with a model-generated summary, checkpoints state, and optionally auto-restarts.

### Components

| Component | Responsibility |
|---|---|
| **SessionPolicy** | Configurable wrapup threshold (default 96%), wall-clock timer (default 30m), auto_restart flag, per-model overrides |
| **SessionController** | Monitors utilization + elapsed time, logs telemetry, triggers graceful finalize |
| **HeadContextManager.graceful_finalize_summary()** | Generates summary of session for next bootstrap |
| **SessionConfig** | Environment overrides (`ANIMUS_SESSION_TIMER_MINUTES`, `ANIMUS_WRAPUP_THRESHOLD`, `ANIMUS_SESSION_AUTO_RESTART`) |

### Lifecycle

```
User interacts with Head REPL
    │
    ▼
After each turn:
  Check utilization ≥ threshold? OR timer expired?
    │
    ├─ No → continue normally
    │
    └─ Yes → inject wrap-up prompt
                │
                ▼
         Call model for session summary
                │
                ▼
         Checkpoint to SQLite
                │
                ▼
         If auto_restart:
           Generate new session_id
           Preserve summary in context
           Bootstrap from checkpoint
                │
                ▼
         Notify user: "Session wrapped. Continuing..."
```

### Configuration

```bash
# Interactive REPL with 30m timer and 96% wrapup
python -m animus_kernel.head --model qwen2.5-coder:14b --session-timer 30m --wrapup-at 0.96

# Via core CLI
animus session --model qwen2.5-coder:14b --timer 30m --wrapup-at 0.96

# Daemon with policy via JSON-RPC
{"method":"initialize","params":{"session_timer_minutes":30,"wrapup_threshold":0.96}}
```

## Implementation

Key technical decisions:

- **Threshold at 96%, not 100%:** Leaves buffer for final summary generation. If threshold is 100%, the summary itself may fail.
- **Model-generated summary:** Uses the same model for summarization as for conversation (configurable). Summary includes key decisions, open threads, facts established.
- **New session_id on restart:** Prevents confusion between old and new context. Old checkpoint is preserved, not overwritten.
- **Telemetry events:** Every wrapup logs utilization, timer status, summary length — data for future policy tuning.

## Evidence

### Tests

- **22 new tests** in `packages/kernel/tests/test_session_controller.py` — all green.
- **83 existing head core tests** unchanged — no regression.
- **Five-session continuity test:** Simulates 5 sequential sessions with SQLite checkpoint persistence. All messages and metadata survive.

### Quality Metrics

| Metric | Target | Actual |
|---|---|---|
| Session crash rate | <1% | 0% since implementation |
| Context bloat incidents | 0 | 0 |
| Manual restart frequency | Reduced | ~80% reduction (estimated from usage patterns) |
| Checkpoint recovery success | >99% | 100% (controlled conditions) |

### Limitations

- **Single-node:** Not validated in distributed or multi-tenant environments.
- **Model-specific:** Summary quality varies by model. Smaller models produce less useful summaries.
- **Timer heuristic:** 30-minute default is arbitrary. Phase 2 Session Steward citizen will analyze telemetry and propose optimal thresholds.

## Tradeoffs

- **Optimized for:** Preventing session crashes and context degradation.
- **Sacrificed:** Session continuity illusion. Users perceive a "restart" even though history is preserved. Some find this jarring.
- **Risk:** Premature wrapup. A 96% threshold with a 30-minute timer may wrap sessions that could have continued productively. Mitigation: Configurable thresholds; per-model overrides.

## Failure Modes

1. **Summary failure:** If the model fails to generate a summary at wrapup, the checkpoint is still saved but the next session has no bootstrap context. Mitigation: Fallback to raw message preservation; summary is convenience, not requirement.
2. **Timer interrupt:** A long-running task (e.g., code generation) may be interrupted by timer expiry. Mitigation: Timer checks occur *after* turn completion, not during.
3. **Checkpoint corruption:** SQLite corruption could prevent session recovery. Mitigation: Backward-compatible schema; corruption triggers fresh session, not crash.
4. **Policy mismatch:** User expects indefinite session; policy wraps at 30m. Mitigation: CLI flags override defaults; daemon RPC allows runtime policy changes.

## Future Work

- **E4 target:** Session Steward citizen (Phase 2) audits telemetry and proposes policy improvements — e.g., "Your 30m timer wastes 23% of context. Recommend 45m."
- **E4 target:** Validate in multi-session environment with concurrent users.
- **E5 target:** Independent reproduction by third-party Ollama deployment.

## Citation

```bibtex
@misc{humanstack-cs004-2026,
  title = {Case Study #004: Session Controller},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E4},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/10-case-studies/CS-004-session-controller.md}
}
```

## References

- [Kernel-Head-Citizen Architecture](../40-architecture/kernel-head-citizen.md)
- [ER-002: Autonomous Code Modification](../70-engineering-reviews/ER-002-autonomous-code-modification.md)
- Animus Session Controller memory: `~/.claude/projects/-home-arete/memory/animus-session-controller.md`

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial case study with SessionController design, test results, and failure modes |
