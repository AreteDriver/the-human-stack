# Field Note #002: Memory Tier Promotion

## Evidence

**Level:** E2
**Confidence:** Low
**Scope:** Narrow
**Status:** Draft
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** Implementation of tiered memory in production system
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Observation

AI systems with persistent memory need tiered storage, but the *mechanism that moves memory between tiers* is rarely specified. Without explicit promotion logic, agents either keep everything hot (context bloat) or lose context permanently.

## Context

In July 2026, Animus specified a HOT/WARM/COLD tier structure but had not defined the promotion mechanism. Competitive analysis of OpenClaw revealed a similar 3-tier architecture with explicit promotion rules. The gap was the transition logic.

## Tier Definitions

| Tier | Storage | TTL Before Review | Promotion Trigger | Demotion Trigger |
|---|---|---|---|---|
| **HOT** | Active context window | Current session | N/A (entry point) | Session end → WARM or discard |
| **WARM** | ChromaDB, fast retrieval | 30 days idle | Accessed 3+ times in 7 days OR explicit tag | 30 days no access → COLD |
| **COLD** | ChromaDB, archival collection | Indefinite | Explicit retrieval request | N/A — permanent unless deleted |

## Promotion Signals

Three signals govern tier assignment:

| Signal | Description |
|---|---|
| **Access frequency** | How often this memory node is retrieved |
| **Recency** | When it was last accessed |
| **Explicit tag** | Human or agent-set priority override |

## Promotion Flow

```
Session ends
    │
    ▼
HOT context reviewed
    │
    ├─ Significant fact / decision / preference detected?
    │       │
    │       └─► Write to WARM with metadata tag
    │
    └─ Transient exchange (small talk, lookups)?
            │
            └─► Discard

Every 30 days (scheduled review):
    │
    ├─ WARM nodes with 0 access in 30 days → promote to COLD
    └─ COLD nodes explicitly retrieved → promote to WARM
```

## Metadata Schema

Every memory node carries:

```json
{
  "id": "uuid",
  "content": "...",
  "tier": "HOT | WARM | COLD",
  "created_at": "ISO8601",
  "last_accessed": "ISO8601",
  "access_count": 0,
  "tags": [],
  "source": "episodic | semantic | procedural",
  "promoted_by": "agent_id | human",
  "signature": "Ed25519 signed by Core"
}
```

## Implementation Status

**Current state:** Specified but not implemented. Requires:
1. ChromaDB running locally with collection separation (HOT/WARM/COLD as distinct collections)
2. Scheduled review process (cron or background task)
3. Decision agent for end-of-session HOT review

**MVP shortcut:** Before full architecture, a cron script reading ChromaDB access logs and moving nodes by threshold gets the behavior without the full orchestration.

## Implication

Tiered memory without promotion logic is just expensive storage. The value is in the *decision* of what to keep, what to archive, and what to discard — not in the storage layers themselves.

## Open Questions

- How do you measure "significant fact" vs "transient exchange" without human review?
- What is the optimal TTL for WARM demotion? 30 days is arbitrary.
- Should promotion be deterministic (rule-based) or learned (pattern-based)?

## References

- [CS-002: Durable Object Store](../../10-case-studies/CS-002-durable-object-store.md)
- [FN-003: Context Compaction](FN-003-context-compaction.md)
- Animus memory gaps spec: `notes/topics/animus-memory-gaps.md`

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial field note with tier definitions, promotion flow, and metadata schema |
