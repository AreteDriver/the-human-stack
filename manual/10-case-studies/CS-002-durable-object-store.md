# Case Study #002: Upstreaming DurableObjectStore

## Evidence

**Level:** E4
**Confidence:** High
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-07
**Review Trigger:** Remote PostgreSQL + replication validation
**Expected Upgrade:** E5
**Owner:** @AreteDriver

---

## Problem

Animus v2.3 used SQLite as its default persistence layer. While sufficient for single-node deployments, it presented three operational risks:

1. **Durability:** SQLite corruption would lose session history and checkpoints.
2. **Scalability:** Concurrent writes from multiple daemon processes caused lock contention.
3. **Observability:** No native support for backup, replication, or point-in-time recovery.

## Context

The `animus-mind` fork had prototyped a `DurableObjectStore` backed by PostgreSQL with bitemporal semantics. The challenge was upstreaming this to v2.3 without disrupting the existing SQLite-based kernel.

Constraints:

- Must remain optional (SQLite remains default for local-first deployments)
- Must preserve all existing test contracts
- Must integrate with the P3 daemon's persistence layer
- Must not introduce PostgreSQL as a hard dependency

## Solution

Extract `DurableObjectStore` from the mind fork, strip bitemporal semantics (transaction-time only), and wire it into v2.3 as an optional backend via configuration.

Key changes during upstreaming:

- Removed valid-time queries (overkill for v2.3 needs)
- Added connection pooling via `asyncpg`
- Preserved content-addressable storage (hash-based deduplication)
- Added migration path: SQLite → PostgreSQL without data loss

## Evidence

### Benchmark

- **Suite:** 21 integration tests
- **Metric:** Persistence latency, throughput, correctness
- **Result:** All tests green. PostgreSQL backend showed 3× faster checkpoint writes under concurrent load compared to SQLite.
- **Conditions:** Single-node with local PostgreSQL instance
- **Limitations:** Not tested with remote PostgreSQL or replication topology

### Regression

- **Kernel suite:** 2928 tests green after upstreaming
- **No breaking changes:** Existing SQLite users unaffected

## Tradeoffs

- **Optimized for:** Durability and operational observability
- **Sacrificed:** Bitemporal queries. The mind fork's valid-time semantics were theoretically elegant but unused in practice.
- **Risk:** PostgreSQL dependency adds operational complexity for users who do not already run a database.

## Failure Modes

1. **Connection pool exhaustion:** Under high concurrency, unbounded connections could overwhelm PostgreSQL. Mitigation: Configurable pool limits with backpressure.
2. **Migration failure:** SQLite → PostgreSQL migration may fail on corrupted source data. Mitigation: Dry-run mode and rollback capability.

## Open Questions

- Is PostgreSQL the right long-term backend, or should the abstraction support multiple backends (e.g., CockroachDB, Yugabyte)?
- At what checkpoint frequency does write amplification become problematic?
- Should bitemporal semantics be reintroduced for compliance use cases (e.g., financial audit trails)?

## Future Work

- **E4 target:** Validate with remote PostgreSQL and replication
- **E5 target:** Community adoption in multi-node deployments

## Citation

```bibtex
@misc{humanstack-cs002-2026,
  title = {Case Study #002: Upstreaming DurableObjectStore},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E4},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/10-case-studies/CS-002-durable-object-store.md}
}
```

## References

- [ER-001: Mind-Class Fork](../70-engineering-reviews/ER-001-mind-class-fork.md)
- [Animus P3 Daemon](../30-operations/animus-p3-daemon.md)

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-07 | Initial case study |
