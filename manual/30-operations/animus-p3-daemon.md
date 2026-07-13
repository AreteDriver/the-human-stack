# Animus P3 Daemon

## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Narrow
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** Daemon crash or resource leak incident
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## What It Is

The P3 Daemon is Animus's persistent local mode: a background process that manages resources, schedules tasks, handles signals, and persists state across sessions.

## Key Components

| Component | Responsibility |
|---|---|
| **ResourceGuard** | Monitors CPU, memory, GPU usage; enforces limits |
| **SessionManager** | Maintains active sessions, handles wrapup/restart |
| **TaskScheduler** | Async DAG scheduling with dependency awareness |
| **Event System** | Pub/sub for inter-component communication |
| **AnimusDaemon** | PID singleton, signal-safe shutdown |

## Evidence

- **36 tests** green in daemon suite
- **PID singleton** verified — no duplicate daemon processes
- **Signal-safe shutdown** — SIGTERM/SIGINT handled gracefully
- **Integration** — Discovery, Session Controller, and other subsystems run as daemon tasks

### Limitations

- **Single-node only** — Not validated in distributed or multi-tenant environments
- **No auto-restart on crash** — If the daemon crashes, background tasks are orphaned until manual restart
- **Resource limits are advisory** — Hard kill not implemented; processes may exceed limits briefly

## Tradeoffs

- **Optimized for:** Persistent, background operation with graceful lifecycle management.
- **Sacrificed:** High availability. Single process; no failover or clustering.
- **Risk:** Orphaned tasks on daemon crash. Mitigation: PID file detection on startup; log orphaned tasks for cleanup.

## References

- [CS-001: Animus P5 Discovery](../10-case-studies/CS-001-animus-p5-discovery.md)
- [Session Controller](../30-operations/session-steward-playbook.md)
- [Kernel-Head-Citizen Architecture](../40-architecture/kernel-head-citizen.md)

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial daemon overview |
