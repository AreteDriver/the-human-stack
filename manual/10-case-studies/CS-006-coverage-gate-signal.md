# Case Study #006: When to Bypass a Red Gate

## Evidence

**Level:** E4
**Confidence:** High
**Scope:** Narrow
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-04-24
**Review Trigger:** Operational incident ( Dependabot backlog )
**Expected Upgrade:** E5
**Owner:** @AreteDriver

---

## Problem

A test coverage gate set at 97% blocked a fleet-wide Dependabot backlog for days. The full test suite — 9,800 tests — was passing. Only the percentage gate was red. The backlog included HIGH-severity patches (xmldom). Every hour the gate stayed red, the vulnerability window stayed open.

## Options Considered

| Option | Time | Risk |
|--------|------|------|
| Hard-grind 342 lines of defensive coverage | 3–4 hours | High context-switch cost; 20–60 min CI feedback loops |
| Keep shipping against red gate | Ongoing | Admin-bypass theater; defeats the purpose of gates |
| Temporarily lower gate to 95% with restoration TODO | 5 minutes | Temporary coverage blind spot; requires discipline to restore |

## Chosen Option

Lowered gate to 95% with an inline TODO marker and a 24-hour restoration check. Within 24 hours, Dependabot backlog drained 9 PRs across 3 repos. Gate restored to 97% after verification.

## Rationale

A gate that stays red for days is a broken signal. The purpose of a gate is to catch regressions, not to create theater. When the underlying health (9,800 passing tests) is good and the gate is the only blocker, the right response is to fix the gate's calibration, not to let it block security patches.

The temporary lower + explicit restoration roadmap is preferable to either grinding coverage under pressure (which produces brittle tests) or bypassing the gate (which teaches the team that gates are optional).

## Tradeoffs Accepted

- **Temporary coverage blind spot:** 2% of code paths were ungated for 24 hours.
- **Process debt:** Required explicit calendar reminder to restore; without it, the lower gate could have become permanent.
- **Precedent risk:** Team could interpret this as "gates are negotiable." Mitigated by documenting the decision as an ADL with explicit revisit conditions.

## Revisit Conditions

- Gate not restored within 48 hours → escalate to process violation.
- Coverage drops below 95% even after restoration → investigate test deletion, not metric drift.
- Similar incident recurs within 30 days → lower gate permanently and investigate systemic test-speed problems.

## Lesson

> The gate is a signal, not a religion. A red signal that stays red becomes noise. The right time to bypass a gate is when the gate itself is the problem.

Engineering teams often fetishize process compliance over outcomes. This is especially dangerous in security contexts, where a red gate blocking a patch is actively harmful. The discipline is not in never bypassing — it is in *knowing when to bypass and documenting why*.

## Implications

1. **Gates need calibration review.** A gate set two years ago may not match current codebase size or test speed.
2. **Temporary bypasses must have restoration deadlines.** Without a deadline, temporary becomes permanent.
3. **The ADL is the safety net.** Bypass decisions logged with revisit conditions can be audited later. Bypasses made in Slack threads cannot.
4. **Measure gate-health separately from code-health.** If your tests pass and your gate fails, the gate is broken.

---

## See Also

- [manual/50-principles/decision-logging.md](../50-principles/decision-logging.md) — The ADL format used to log this decision
- [manual/30-operations/runbook-gate-calibration.md](../30-operations/runbook-gate-calibration.md) — Quarterly gate calibration procedure
