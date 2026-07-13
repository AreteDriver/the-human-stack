# MCP Integration Pattern

## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Narrow
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** New MCP server added or protocol revision
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Problem

Integrating tools into an AI agent platform requires manual registration, schema validation, and capability declaration. Each MCP server, OpenAPI endpoint, or annotated script is a separate integration task. The process is opaque, error-prone, and creates schema drift as tools evolve.

## Context

In mid-2026, Animus had 34 MCP tools exposed to Claude Code. Adding a new tool required:
1. Manual schema registration
2. Capability declaration
3. Validation against expected interface
4. Documentation update

Average integration time: ~3 days per tool, requiring deep familiarity with Animus internals.

## Solution

Build a Discovery subsystem with three components:

1. **MCP Scanner** — Discovers MCP servers via filesystem and network scanning
2. **OpenAPI Ingestor** — Parses OpenAPI specs and generates tool manifests
3. **Annotated Script Discovery** — Identifies `@animus.tool` decorators in Python modules

All discovered tools pass through a **4-dimension schema validator** before registration. A **DiscoveryOrchestrator** coordinates scanning, deduplicates by content hash, and persists results via the daemon.

## Architecture

```
DiscoveryOrchestrator
    │
    ├── MCP Scanner ──→ Filesystem scan ──→ MCP manifest
    │
    ├── OpenAPI Ingestor ──→ Spec parse ──→ Tool manifest
    │
    └── Annotated Script Discovery ──→ AST walk ──→ Tool manifest
                │
                ▼
        4-Dimension Schema Validator
                │
                ├── Type safety
                ├── Parameter completeness
                ├── Return contract
                └── Error handling
                │
                ▼
        Hash-based deduplication
                │
                ▼
        Registration via P3 Daemon
```

## Key Technical Decisions

### Hash-Based Deduplication

Tool manifests are hashed by content. If a tool's schema changes, the hash changes, triggering re-validation. This prevents stale registrations.

```python
import hashlib

def hash_manifest(manifest: dict) -> str:
    canonical = json.dumps(manifest, sort_keys=True, ensure_ascii=False)
    return hashlib.sha256(canonical.encode()).hexdigest()
```

### Daemon Integration

Discovery runs as a background task managed by the P3 daemon, not inline with user requests. Async DAG scheduler with dependency awareness:

```
scan → validate → deduplicate → register
```

If validation fails, registration is skipped and the failure is logged.

### 4-Dimension Validation

| Dimension | Check | Failure Mode |
|---|---|---|
| **Type safety** | All parameters have declared types | `schema_drift` |
| **Parameter completeness** | Required params are present, no orphans | `contract_violation` |
| **Return contract** | Return type declared and consistent | `schema_drift` |
| **Error handling** | Error cases documented in schema | `missing_constraint` |

## Evidence

### Benchmark

- **Suite:** 41 unit tests + 153 integration tests (P0–P5 cumulative)
- **Metric:** Tool integration time
- **Result:** Reduced from ~3 days (manual) to ~20 minutes (automated discovery + validation)
- **Conditions:** Single-node development environment
- **Limitations:** Not yet validated under multi-tenant production workloads

### Quality Metrics

| Metric | Target | Actual |
|---|---|---|
| Test coverage | >80% | 194 tests green |
| Schema validation accuracy | >95% | 100% (controlled conditions) |
| False positive registrations | <5% | 0% (controlled conditions) |

## Tradeoffs

- **Optimized for:** Developer experience and integration velocity.
- **Sacrificed:** Granular control. Users can disable discovery per-namespace, but cannot selectively approve individual tools within a discovered batch.
- **Risk:** Over-registration. If a namespace contains many low-quality tools, the validator may not catch semantic issues (only structural ones).

## Failure Modes

1. **Hash collision:** Extremely unlikely with SHA-256, but if two distinct tools produce the same hash, one would be silently skipped. Mitigation: Include tool name in hash input.
2. **Validator bypass:** The 4-dimension validator checks structure, not behavior. A tool with valid schema but runtime exceptions would still register.
3. **Daemon deadlock:** If the daemon crashes during discovery, orphaned scan tasks may accumulate. Mitigation: PID singleton and signal-safe shutdown (P3).

## Open Questions

- Does automated discovery reduce or increase the risk of supply-chain attacks?
- At what scale does hash deduplication become a bottleneck?
- Should discovery be opt-in (explicit registration) or opt-out (automatic with blocklist) for enterprise deployments?

## Future Work

- **E4 target:** Validate discovery under multi-tenant production workloads.
- **E5 target:** External reproduction by third-party Animus deployments.
- **Known unknown:** Performance of hash deduplication at 10,000+ tool scale.

## Citation

```bibtex
@misc{humanstack-arch-mcp-2026,
  title = {MCP Integration Pattern},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E3},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/40-architecture/mcp-integration-pattern.md}
}
```

## References

- [CS-001: Animus P5 Discovery](../10-case-studies/CS-001-animus-p5-discovery.md) — Full case study
- [Kernel-Head-Citizen Architecture](kernel-head-citizen.md)
- [Eval Checkpoint Protocol](../30-operations/eval-checkpoint-protocol.md)

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial pattern with 3 discovery methods, 4-dimension validator, and hash deduplication |
