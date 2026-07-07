# Case Study #003: Upstreaming PolicyDecisionPoint

## Evidence

**Level:** E4
**Confidence:** High
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-07
**Owner:** @AreteDriver

---

## Problem

Animus v2.3 lacked a unified security model. Access control was distributed across individual tools and citizens, leading to:

1. **Inconsistent enforcement:** Some tools checked permissions; others did not.
2. **Audit gaps:** No central log of who accessed what.
3. **Policy drift:** As new tools were added, security rules were often forgotten.

## Context

The `animus-mind` fork had prototyped a `PolicyDecisionPoint` (PDP) with a `CapabilityGrantStore` — a deterministic, default-deny authorization system. Upstreaming required integrating this into v2.3's existing tool-calling pipeline without breaking existing unsecured tools.

Constraints:

- Must be opt-in for existing tools (backward compatibility)
- Must support capability-based access control (not just role-based)
- Must be auditable (every decision logged)
- Must be deterministic (no probabilistic policy evaluation)

## Solution

Upstream `PolicyDecisionPoint` + `CapabilityGrantStore` into v2.3 as a centralized authorization layer:

- **Default-deny:** All tool calls denied unless explicitly granted
- **Capability grants:** Fine-grained permissions (e.g., "citizen-001 may invoke `animus_architect_scan` between 09:00 and 17:00")
- **Deterministic evaluation:** Rule engine produces the same decision for the same inputs, every time
- **Audit trail:** Every grant, revocation, and decision logged to the DurableObjectStore

## Evidence

### Benchmark

- **Suite:** 16 adversarial tests
- **Metric:** Authorization correctness under attack scenarios
- **Result:** 100% correct decisions. No false positives or false negatives in adversarial suite.
- **Conditions:** Single-node deployment with synthetic attack patterns
- **Limitations:** Not yet validated under real-world intrusion attempts

### Integration

- **Regression suite:** 2928 tests green after upstreaming
- **Breaking changes:** None for existing tools (opt-in)

## Tradeoffs

- **Optimized for:** Security correctness and auditability
- **Sacrificed:** Convenience. Default-deny means every new tool requires explicit grant configuration before use.
- **Risk:** Policy misconfiguration. If a legitimate workflow is not granted, it fails silently (denied). Good for security, bad for UX.

## Failure Modes

1. **Policy bloat:** As capabilities accumulate, grant stores become unwieldy. Mitigation: Hierarchical grants and expiration dates.
2. **Performance degradation:** Rule evaluation adds latency to every tool call. Mitigation: Cached decisions with TTL.
3. **Audit log exhaustion:** High-frequency tool calls generate massive audit logs. Mitigation: Sampling and log rotation.

## Future Work

- **E4 target:** Validate under real-world multi-tenant deployment
- **E5 target:** Independent security audit by third party

## References

- [ER-001: Mind-Class Fork](../70-engineering-reviews/ER-001-mind-class-fork.md)
- [Animus P3 Daemon](../30-operations/animus-p3-daemon.md)
- [Capability-Based Access Control Pattern](../40-architecture/capability-based-access-control.md)

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-07 | Initial case study |
