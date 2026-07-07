# Constitution

This document is the editorial policy of The Human Stack. It governs what is included, what is excluded, and what standards every contribution must meet.

## Mission

Build the most evidence-based public reference on engineering operational AI systems.

## Scope

### Included

- Deployment methodologies
- Evaluation frameworks
- Operational playbooks and runbooks
- Governance models
- Architecture decisions and tradeoffs
- Case studies with measured outcomes
- Engineering reviews (successes, failures, reversals)
- Benchmark reports
- Failure analyses and postmortems
- Architecture patterns
- Research with reproducible methodology

### Excluded

- Prompt collections (implementation detail, not engineering principle)
- AI hype, speculative AGI predictions, or futurism
- AI news commentary or model release reactions
- Vendor marketing or unverified product claims
- Content without evidence or reproducible methodology

## Rules

### 1. Every technical claim must be evidence-backed or explicitly labeled as a hypothesis.

If you cannot provide evidence, you must label the claim:

```
Hypothesis: We believe X.
Evidence: None. Planned evaluation: [describe].
```

### 2. Every chapter must carry an evidence block.

See [EVIDENCE.md](EVIDENCE.md) for the format. At minimum:

- Evidence Level (E0–E5)
- Confidence (Low / Medium / High)
- Status (Draft / Stable / Archived)
- Version
- Last Review date
- Owner (person or team responsible for accuracy)

### 3. Every architecture discussion must include tradeoffs, rejected alternatives, and failure modes.

A design without tradeoffs is not engineering. It is preference.

Required sections:

- **Tradeoffs:** What did you optimize for? What did you sacrifice?
- **Rejected Alternatives:** What did you consider and discard? Why?
- **Failure Modes:** How does this break? What are the failure signatures?

### 4. Every idea must have a revision history.

Ideas evolve. The manual must make that evolution visible, not hide it.

Required:

- **Version history:** What changed and when.
- **Changelog:** Brief summary of major revisions.
- **Future work:** What remains unverified or unknown.

### 5. Engineering Reviews are first-class content.

Successes, failures, reversals, and abandoned designs all teach. No chapter is complete without acknowledging what was tried and discarded.

### 6. Reference implementations are evidence, not the product.

Animus, drift-monitor, memboot, and other linked projects exist to prove that the principles in this manual work. The manual does not exist to document those projects.

### 7. No isolated chapters.

Every chapter must reference related chapters. The manual is a graph, not a list.

### 8. Prefer measured claims.

Avoid:
- "revolutionary"
- "game-changing"
- "world-first" (unless independently verifiable)
- "solves AI"
- "best"

Prefer:
- specific evidence
- tradeoffs
- limitations
- reproducible results
- observations over assertions

## Editorial Process

1. **Draft:** Written by contributor. Evidence level set. Review requested.
2. **Technical Review:** At least one reviewer validates evidence, logic, and reproducibility.
3. **Evidence Check:** Evidence level confirmed or challenged.
4. **Publish:** Merged to manual. Evidence level displayed prominently.
5. **Re-review:** All chapters with evidence level < E4 must be re-reviewed every 6 months.

## Amendments

This constitution may be amended via pull request with:
- Proposed change
- Rationale
- Impact analysis on existing chapters
- Approval by two maintainers
