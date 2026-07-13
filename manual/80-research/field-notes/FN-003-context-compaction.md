# Field Note #003: Context Compaction

## Evidence

**Level:** E2
**Confidence:** Low
**Scope:** Narrow
**Status:** Draft
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** First implementation of lossless context compaction
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Observation

When the active context window approaches its limit, older messages vanish silently. The agent loses information without knowing it lost it. "Lossless" compaction requires an explicit spec for: what gets summarized, by whom, stored where, and how it's flagged for re-expansion.

## Context

In July 2026, Animus Head Phase 3 implemented context pruning (removing old messages to stay under token limits) but not compaction (summarizing old messages while preserving originals). The result was information loss without recovery.

## Trigger

Compaction fires when active context reaches **75% of model context window limit**.

```yaml
compaction:
  trigger_threshold: 0.75   # % of context window
  summary_model: "same"     # use current active model for summarization
  max_summary_tokens: 512   # per compacted block
  expansion_on_demand: true
```

## Compaction Process

```
Context window at 75%
    │
    ▼
Identify compaction candidates:
  - Messages older than N turns (default: 10)
  - Already-resolved sub-tasks
  - Non-critical exchanges
    │
    ▼
Generate summary block:
  - Key decisions made
  - Facts established
  - Open threads still active
  - Timestamp range covered
    │
    ▼
Summary block written to WARM memory with metadata:
  - type: "compaction_summary"
  - covers_turns: [start, end]
  - expansion_available: true
  - original_turns_ref: [message_ids]
    │
    ▼
Original turns moved to COLD (not deleted)
    │
    ▼
Summary injected into active context as single node
  "[COMPACTED: turns 1-10 — see memory node uuid for expansion]"
```

## Expansion on Demand

When agent or user references something in a compacted block:

```
User: "What did we decide about X earlier?"
    │
    ▼
Retrieval detects reference to compacted range
    │
    ▼
COLD fetch of original turns OR summary expansion
    │
    ▼
Inject relevant excerpt back into context
  "[EXPANDED from compaction uuid]"
```

## Integrity Guarantee

- Original turns are **never deleted**, only moved to COLD
- Compaction summaries are signed at write time
- Expansion always available via explicit retrieval
- Compaction event logged to signal bus

## "Lossless" Definition

> No information is permanently destroyed. Compression is lossy by definition, but the originals are preserved and retrievable. "Lossless" in this spec means: *no unrecoverable information loss*.

This is the honest version of what some systems call "lossless." Summaries lose nuance. The originals don't.

## Implementation Status

**Current state:** Specified but not implemented. Phase 1 of Session Controller uses pruning (removal) rather than compaction (summary + archive). Compaction is planned for Phase 3.

## Implication

Context limits are not just a technical constraint — they are an operational risk. A full context window behaves differently than an empty one. Systems that silently drop old context without compaction create invisible information loss that compounds over time.

## Open Questions

- What is the optimal summary length? 512 tokens is arbitrary.
- Should compaction be automatic or agent-initiated?
- How do you detect when a summary is "stale" and needs re-generation?

## References

- [FN-002: Memory Tier Promotion](FN-002-memory-tier-promotion.md)
- [CS-004: Session Controller](../../10-case-studies/CS-004-session-controller.md)
- Animus memory gaps spec: `notes/topics/animus-memory-gaps.md`

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial field note with compaction trigger, process, expansion, and integrity guarantees |
