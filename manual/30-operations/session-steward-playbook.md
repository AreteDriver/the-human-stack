# Session Steward Playbook

## Evidence

**Level:** E2
**Confidence:** Low
**Scope:** Narrow
**Status:** Draft
**Version:** 0.5
**Last Review:** 2026-07-12
**Review Trigger:** Session telemetry reaches 30-day threshold
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Problem

Session policies (wrapup threshold, timer duration, auto-restart) are configured manually and reviewed rarely. A 30-minute timer may be optimal for one workflow and wasteful for another. A 96% context threshold may wrap sessions that could have continued productively. Without telemetry analysis, policy tuning is guesswork.

## Context

The Session Controller (see [CS-004: Session Controller](../10-case-studies/CS-004-session-controller.md)) wraps sessions at configurable thresholds. Phase 1 implemented the controller. Phase 2 adds a **Session Steward** — a retrospective audit system that analyzes telemetry and proposes policy improvements.

**Phase 1 status:** Implemented 2026-07-06. 22 tests green. Session telemetry is logged to SQLite.

## Solution

The Session Steward is a **retrospective analysis layer**, not a real-time controller. It operates on accumulated telemetry to answer questions and propose changes.

### Heuristics (Planned)

| ID | Heuristic | Question | Action if True |
|---|---|---|---|
| H1 | Timer waste | Does the 30m timer fire when utilization <50%? | Recommend longer timer or utilization-only wrapup |
| H2 | Threshold tightness | Are sessions wrapping at >90% utilization consistently? | Recommend lower threshold to leave buffer |
| H3 | Restart fatigue | Do users manually restart within 5 minutes of auto-restart? | Recommend disabling auto-restart for that workflow |
| H4 | Model variance | Does one model consistently hit thresholds faster than others? | Recommend model-specific policy adjustment |
| H5 | Time-of-day bias | Are wrapups concentrated at certain hours? | Recommend scheduling-based policies |
| H6 | Context quality | Does summary quality degrade over session age? | Recommend shorter timer for older sessions |
| H7 | Silence waste | Are long idle periods followed by wrapup? | Recommend idle timeout in addition to wall-clock timer |
| H8 | Cost efficiency | What percentage of context window is actually used before wrapup? | Recommend threshold adjustment |

### Steward Lifecycle

```
Session telemetry accumulates in SQLite
    │
    ▼
Weekly (or on-demand):
  SessionSteward.analyze()
    │
    ▼
Run heuristics H1–H8 against telemetry
    │
    ▼
Generate PolicyDiff (current → proposed)
    │
    ▼
Human reviews proposal
    │
    ├─ Reject → log reason, keep current policy
    │
    └─ Approve → apply policy update
```

## Implementation

### Planned Components

| Component | Status | Description |
|---|---|---|
| **TelemetryProvider** | Not started | Protocol for reading session telemetry from SQLite |
| **SessionSteward** | Not started | Citizen that runs heuristics and generates PolicyDiff |
| **PolicyDiff** | Not started | Structured proposal: what changed, why, expected impact |
| **Daemon integration** | Not started | Weekly cron trigger via P3 daemon |
| **Audit history** | Not started | Persistent log of all policy changes and rationale |

### Configuration

```yaml
session_steward:
  enabled: false  # Phase 2 — not yet active
  schedule: "0 9 * * 1"  # Weekly, Monday 9am
  heuristics:
    - H1_timer_waste
    - H2_threshold_tightness
    - H3_restart_fatigue
    - H4_model_variance
  min_sessions_before_analysis: 10
  confidence_threshold: 0.80  # Only propose if heuristic confidence >80%
```

## Evidence

### Current State

- **Phase 1 complete:** SessionController with telemetry logging
- **Phase 2 planned:** SessionSteward citizen with 8 heuristics
- **Telemetry data:** Minimal (1 week of operation as of 2026-07-12)
- **No live analysis yet:** Insufficient data for meaningful heuristic output

### Expected Evidence Requirements

Before Session Steward can claim E4:

1. **≥30 days of telemetry** from real usage
2. **≥1 policy change** proposed and approved based on heuristic output
3. **Measurable improvement** post-change (e.g., reduced wrapup waste, fewer manual restarts)

## Tradeoffs

- **Optimized for:** Evidence-based policy tuning without human micromanagement.
- **Sacrificed:** Responsiveness. Weekly analysis means policy adjustments lag behind usage pattern changes.
- **Risk:** Overfitting. Heuristics tuned to one user's patterns may not generalize.

## Failure Modes

1. **Premature proposal:** With insufficient data, heuristics produce spurious recommendations. Mitigation: `min_sessions_before_analysis` threshold.
2. **Cascading policy changes:** Approved policy change triggers new telemetry patterns, which trigger new proposals. Mitigation: Cooldown period (min 7 days between policy changes).
3. **Human override abandonment:** If every proposal is rejected, the steward becomes noise. Mitigation: Track acceptance rate; disable if <20%.

## Future Work

- **Phase 2 target:** Implement TelemetryProvider + SessionSteward with H1–H4 (core heuristics).
- **Phase 3 target:** Add H5–H8, daemon integration, audit history persistence.
- **E4 target:** First policy change proposed and approved based on 30+ days of telemetry.
- **E5 target:** Independent reproduction on different user's session data.

## Citation

```bibtex
@misc{humanstack-ops-steward-2026,
  title = {Session Steward Playbook},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {0.5},
  evidence = {E2},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/30-operations/session-steward-playbook.md}
}
```

## References

- [CS-004: Session Controller](../10-case-studies/CS-004-session-controller.md) — Phase 1 implementation
- [Kernel-Head-Citizen Architecture](../40-architecture/kernel-head-citizen.md)
- [ER-002: Autonomous Code Modification](../70-engineering-reviews/ER-002-autonomous-code-modification.md) — Related citizen architecture

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 0.5 | 2026-07-12 | Initial playbook with 8 heuristics, planned components, and evidence targets. Phase 2 not yet implemented. |
