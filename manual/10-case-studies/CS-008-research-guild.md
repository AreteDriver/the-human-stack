# Case Study #008: Research Guild — Separating Research from Execution

## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** Citizen pipeline validation
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Problem

When an autonomous system consumes raw external sources (papers, repos, podcasts, news) directly for implementation, two failure modes emerge:

1. **Noise amplification:** Forge (the executor) drowns in implementation details, version-specific syntax, and transient frameworks. It copies rather than abstracts.
2. **Architecture drift:** Without a separation between "what principle is being demonstrated" and "how should we implement it," every new source risks adding a bespoke solution rather than a structural improvement.

The challenge: most engineering organizations separate Research, Architecture, and Engineering. In an autonomous system, these roles collapse into a single agent — and the agent defaults to the easiest path (copy the source).

## Context

Animus had established Forge as its execution layer (planning, implementation, verification, deployment). But Forge was being fed raw sources directly — GitHub repos, technical papers, API docs — with no intermediate abstraction layer.

This created a tension:
- Forge is optimized for *doing* — feasibility analysis, task planning, code generation.
- Forge is *not* optimized for *understanding* — philosophical synthesis, mechanism extraction, pattern discovery, first-principles reasoning.

The solution required a dedicated pipeline between "external observation" and "execution" — a Research Guild.

## Solution

Define a six-stage citizen pipeline that transforms raw sources into actionable proposals. Each stage is a distinct citizen with its own evaluation criteria. Output is **ideas**, not code.

```
Raw Source (paper, repo, podcast, conversation)
    │
    ▼
Harvester
    │  collects, deduplicates, tags
    ▼
Abstraction Citizen
    │  extracts mechanisms, discards implementation details
    ▼
Pattern Citizen
    │  finds recurring structures across abstractions
    ▼
First-Principles Citizen
    │  reduces patterns to fundamental engineering truths
    ▼
Architecture Citizen
    │  drafts concrete proposals for system adoption
    ▼
Forge
    │  evaluates feasibility, plans implementation, executes safely
    ▼
Evidence Bundle → Human Approval → Merge
```

## Implementation

### Guild Members

| Citizen | Role | Output | Evaluation |
|---------|------|--------|------------|
| **Harvester** | Collect raw sources | Tagged source bundle | Coverage rate vs. human baseline |
| **Abstraction** | Extract transferable mechanisms | Mechanism card | Human adjudication: correct / partial / wrong |
| **Pattern** | Find recurring structures | Pattern card | Verified across ≥3 independent sources |
| **First-Principles** | Reduce to timeless principles | Principle card | Adversarial challenge pass rate |
| **Architecture** | Draft adoption proposals | Improvement Proposal | Proposal → merge rate |

### Governance Rules

1. **Guild output is proposals, not directives.** No Guild member can command Forge.
2. **Proposals require human approval** before entering Forge pipeline.
3. **Guild members may not modify memory directly.** They read corpus, write proposals.
4. **Every proposal must cite its lineage:** source → abstraction → pattern → principle → proposal.
5. **Contradictions are surfaced, not resolved silently.** First-Principles flags conflicts for human adjudication.

### Abstraction Example

- **Source:** "Company X uses Redis for distributed caching."
- **Abstraction:** "Separation of state from computation."
- **Pattern:** "State externalization enables resilience and portability."
- **First Principle:** "Systems that separate concerns survive longer than systems that conflate them."
- **Architecture Proposal:** "Add checkpoint externalization to session persistence layer."

## Evidence

### Benchmark

- **Suite:** 41 tests covering all 5 Guild citizens + integration tests for Orchestrator
- **Metric:** Proposal quality and merge rate
- **Result:** 194 total P0–P5 tests green; Research Guild proposals enter Forge with full lineage metadata
- **Conditions:** Single-node development environment with controlled source corpus
- **Limitations:** Not yet validated at scale (10,000+ raw sources)

### Quality Metrics

| Metric | Target | Actual |
|--------|--------|--------|
| Test coverage | >80% | 41 unit + 153 integration tests green |
| Mechanism extraction accuracy | >90% | Human-verified samples (controlled) |
| Pattern verification | ≥3 independent sources | Met (controlled corpus) |
| Proposal → merge rate | Track | Baseline established, awaiting 6-month data |

## Tradeoffs

- **Optimized for:** Signal-to-noise ratio in the architecture-improvement pipeline
- **Sacrificed:** Latency. A source that enters the Guild may take hours or days to emerge as a proposal, compared to direct ingestion by Forge.
- **Sacrificed:** Coverage. The Guild may miss opportunistic "quick fixes" that don't warrant full pipeline traversal.

## Failure Modes

1. **Abstraction loss:** If Abstraction Citizen is too aggressive, it strips actionable detail. If too conservative, it passes implementation baggage downstream. Mitigation: Human spot-checks on mechanism cards.
2. **Pattern hallucination:** Finding "recurring structures" that are actually coincidence. Mitigation: Minimum 3 independent sources per pattern; adversarial challenge.
3. **Pipeline stall:** If any citizen fails, the entire proposal stalls. Mitigation: Graceful degradation — each stage publishes partial output; Forge can act on incomplete lineage with human override.

## Open Questions

- At what corpus size does the Guild become a bottleneck rather than a filter?
- Should the Guild be opt-in (explicitly route sources through it) or opt-out (all sources pass through by default)?
- Can Guild citizens be externalized as standalone services for other projects?

## Future Work

- **E4 target:** Validate Guild at scale — 500+ repos, 20,000+ papers, continuous monitoring
- **E5 target:** Self-improving Guild — citizens detect their own extraction weaknesses and propose architectural improvements to themselves
- **Known unknown:** Performance of pattern clustering at 100,000+ mechanism scale

## Citation

```bibtex
@misc{humanstack-cs008-2026,
  title = {Case Study #008: Research Guild — Separating Research from Execution},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E3},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/10-case-studies/CS-008-research-guild.md}
}
```

## References

- [Case Study #007: Machine Character](CS-007-machine-character.md)
- [Animus Evidence Framework](../20-fundamentals/evidence-grading.md)
- [Decision Logging Methodology](../50-principles/decision-logging.md)

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-22 | Initial case study |
