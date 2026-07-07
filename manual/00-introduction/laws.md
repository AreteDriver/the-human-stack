# The Laws of Operational AI

## Evidence

**Level:** E1
**Confidence:** Medium
**Status:** Draft
**Version:** 1.0
**Last Review:** 2026-07-07
**Owner:** @AreteDriver

---

These laws are not universal truths. They are design heuristics that emerge from treating AI systems as operational systems. They are falsifiable — each law is stated in a form that can be tested against evidence.

---

## Law 1

### Every AI system eventually becomes an operational system.

A model deployed once is an experiment. A model deployed continuously, with users depending on it, with drift to monitor, with failures to respond to — that is an operational system. The transition happens regardless of whether you planned for it.

**Implication:** Design for operations from the start, not as an afterthought.

**Evidence:** See [CS-001: Animus P5 Discovery](../10-case-studies/CS-001-animus-p5-discovery.md) — tool discovery went from manual integration (experimental) to automated, governed, monitored infrastructure (operational).

---

## Law 2

### Every operational AI system accumulates invisible state.

The prompt that worked yesterday may fail today because the model updated, the context window filled, or the user's expectations shifted. This state is invisible until it causes a failure.

**Implication:** You cannot reason about an AI system by looking at its code. You must observe its behavior over time.

**Evidence:** See [ER-001: Mind-Class Fork](../70-engineering-reviews/ER-001-mind-class-fork.md) — the fork drifted from mainline precisely because its state (context, dependencies, conventions) was not continuously reconciled.

---

## Law 3

### Every deployment creates new failure modes.

Deploying a system does not just expose it to users. It creates new interactions, new edge cases, and new ways to fail that were not present in testing. The combinatorial space of production is larger than the combinatorial space of testing.

**Implication:** Expect failures. Design for detection and response, not prevention.

**Evidence:** See [CS-003: Policy Decision Point](../10-case-studies/CS-003-policy-decision-point.md) — default-deny authorization was added because every new tool created a new attack surface.

---

## Law 4

### Evaluation is continuous, not a milestone.

You do not "test" an AI system before release. You instrument it for continuous evaluation. Models drift. Data distributions shift. User behavior changes. Evaluation that stops at deployment is evaluation that becomes false within weeks.

**Implication:** Build evaluation infrastructure as a first-class system component, not a CI gate.

**Evidence:** See [Benchmark Methodology](../20-evaluation/benchmark-methodology.md) — weekly recalibration against baselines, drift detection when scores drop >5%.

---

## Law 5

### Memory is operational infrastructure.

An AI system without memory of its past decisions, failures, and tradeoffs cannot improve. Memory is not a feature — it is the foundation of operational learning.

**Implication:** Treat memory systems (checkpoints, logs, decision records) with the same rigor as compute and storage.

**Evidence:** See [CS-002: Durable Object Store](../10-case-studies/CS-002-durable-object-store.md) — SQLite replaced by PostgreSQL-backed persistence because durability and observability are operational requirements.

---

## Corollaries

### From Law 1 (Every AI system becomes operational)

**Corollary 1a:** The cost of operationalizing an experimental system is higher than designing it as operational from the start.

**Corollary 1b:** Systems designed only for demos will fail when they become operational. Plan for the transition, or it will happen to you.

### From Law 2 (Invisible state accumulates)

**Corollary 2a:** Context windows are not just technical constraints — they are operational risks. A full context window behaves differently than an empty one.

**Corollary 2b:** Model updates are not improvements — they are perturbations. Every update requires re-evaluation.

### From Law 3 (Deployments create failure modes)

**Corollary 3a:** The most dangerous failure mode is the one you did not test for. Design detection, not just prevention.

**Corollary 3b:** Rollback capability is more important than deployment speed.

### From Law 4 (Evaluation is continuous)

**Corollary 4a:** Benchmarks older than the current model version have reduced evidentiary value. Re-evaluation is not optional.

**Corollary 4b:** Evaluation metrics must include operational indicators (latency, error rate, user satisfaction), not just accuracy.

### From Law 5 (Memory is infrastructure)

**Corollary 5a:** A system without decision records cannot be audited. A system without audit trails cannot be trusted.

**Corollary 5b:** Memory that cannot be inspected is not memory — it is a black box.

---

## Open Questions

- Are there additional laws? (Hypothesis: at least 2 more related to governance and human oversight)
- Can Law 1 be falsified? What would constitute evidence that an AI system *did not* become operational?
- Do these laws apply to non-AI operational systems? (Lean manufacturing, SRE) Or are they specific to the probabilistic nature of AI?

## References

- [CS-001: Animus P5 Discovery](../10-case-studies/CS-001-animus-p5-discovery.md)
- [CS-002: Durable Object Store](../10-case-studies/CS-002-durable-object-store.md)
- [CS-003: Policy Decision Point](../10-case-studies/CS-003-policy-decision-point.md)
- [ER-001: Mind-Class Fork](../70-engineering-reviews/ER-001-mind-class-fork.md)
- [Benchmark Methodology](../20-evaluation/benchmark-methodology.md)

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-07 | Initial laws and corollaries |
