# Case Study #001: Animus P5 Discovery

## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-07
**Review Trigger:** Multi-tenant production validation
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Problem

As the Animus ecosystem grew, integrating new tools into the agent platform became a bottleneck. Each MCP server, OpenAPI endpoint, or annotated script required manual registration, schema validation, and capability declaration. The process took approximately 3 days per tool and required deep familiarity with Animus internals.

This created two failure modes:

1. **Adoption friction:** Users avoided integrating tools because the process was opaque.
2. **Schema drift:** Manual registration meant schemas often diverged from the source of truth as tools evolved.

## Context

Animus P0–P4 had established the core platform (kernel, head, daemon, session stewardship) but assumed tools were known at build time. P5 was scoped to address runtime tool discovery and integration.

Constraints:

- Must support MCP, OpenAPI, and annotated Python scripts
- Must validate schemas before registration (4-dimension check)
- Must deduplicate tools to prevent redundant registrations
- Must integrate with the P3 daemon for background scanning
- Must not require manual intervention for known tool types

## Solution

Build a Discovery subsystem with three components:

1. **MCP Scanner:** Discovers MCP servers via filesystem and network scanning
2. **OpenAPI Ingestor:** Parses OpenAPI specs and generates tool manifests
3. **Annotated Script Discovery:** Identifies `@animus.tool` decorators in Python modules

All discovered tools pass through a **4-dimension schema validator** (type safety, parameter completeness, return contract, error handling) before registration. A **DiscoveryOrchestrator** coordinates scanning, deduplicates by content hash, and persists results via the daemon.

## Implementation

Key technical decisions:

- **Hash-based deduplication:** Tool manifests are hashed by content. If a tool's schema changes, the hash changes, triggering re-validation. This prevents stale registrations.
- **Daemon integration:** Discovery runs as a background task managed by the P3 daemon, not inline with user requests.
- **Async DAG scheduler:** Discovery tasks are scheduled with dependency awareness (e.g., validate before register).

## Evidence

### Benchmark

- **Suite:** 41 unit tests + 153 integration tests (P0–P5 cumulative)
- **Metric:** Tool integration time
- **Result:** Reduced from ~3 days (manual) to ~20 minutes (automated discovery + validation)
- **Conditions:** Single-node development environment
- **Limitations:** Not yet validated under multi-tenant production workloads

### Quality Metrics

| Metric | Target | Actual |
|--------|--------|--------|
| Test coverage | >80% | 194 tests green |
| Schema validation accuracy | >95% | 100% (controlled conditions) |
| False positive registrations | <5% | 0% (controlled conditions) |

## Tradeoffs

- **Optimized for:** Developer experience and integration velocity
- **Sacrificed:** Granular control over individual tool registrations. Users can disable discovery per-namespace, but cannot selectively approve individual tools within a discovered batch.
- **Risk:** Over-registration. If a namespace contains many low-quality tools, the validator may not catch semantic issues (only structural ones).

## Failure Modes

1. **Hash collision:** Extremely unlikely with SHA-256, but if two distinct tools produce the same hash, one would be silently skipped. Mitigation: Include tool name in hash input.
2. **Validator bypass:** The 4-dimension validator checks structure, not behavior. A tool with valid schema but runtime exceptions would still register.
3. **Daemon deadlock:** If the daemon crashes during discovery, orphaned scan tasks may accumulate. Mitigation: PID singleton and signal-safe shutdown (P3).

## Open Questions

- Does automated discovery reduce or increase the risk of supply-chain attacks? (If tools are auto-discovered from external sources, validation becomes critical.)
- At what scale does hash deduplication become a bottleneck?
- Should discovery be opt-in (explicit registration) or opt-out (automatic with blocklist) for enterprise deployments?

## Future Work

- **E4 target:** Validate discovery under multi-tenant production workloads
- **E5 target:** External reproduction by third-party Animus deployments
- **Known unknown:** Performance of hash deduplication at 10,000+ tool scale

## Citation

```bibtex
@misc{humanstack-cs001-2026,
  title = {Case Study #001: Animus P5 Discovery},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E3},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/10-case-studies/CS-001-animus-p5-discovery.md}
}
```

## References

- [ER-001: Mind-Class Fork](../70-engineering-reviews/ER-001-mind-class-fork.md)
- [Animus P3 Daemon](../30-operations/animus-p3-daemon.md)
- [MCP Integration Pattern](../40-architecture/mcp-integration-pattern.md)

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-07 | Initial case study |
