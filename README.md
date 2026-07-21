# The Human Stack

> **AI systems should be engineered as operational systems.**

**This repository distinguishes between hypotheses, implementations, and evidence. Not every idea here is mature. Every claim is versioned, evidence-graded, and open to revision as new data emerges.**

---

## What This Is

The Human Stack is a living engineering reference for deploying, operating, and evaluating AI systems in production. It is not documentation for a single product. It is not a blog. It is a public research lab: a continuously revised body of engineering knowledge where claims become stronger as evidence accumulates.

## Start Here

1. **[VISION.md](VISION.md)** — What this project is and why it exists.
2. **[CONSTITUTION.md](CONSTITUTION.md)** — The editorial rules that govern every page.
3. **[EVIDENCE.md](EVIDENCE.md)** — How we grade claims and what each level means.
4. **[manual/START-HERE.md](manual/START-HERE.md)** — Guided path through the chapters.

## Structure

| Directory | Purpose |
|-----------|---------|
| `manual/` | The living engineering reference — evidence-graded, versioned, continuously revised. |
| `templates/` | Standard formats for chapters, reviews, benchmarks, and postmortems. |
| `references/` | Links to reference implementations (Animus, drift-monitor, memboot, etc.). |
| `EVIDENCE.md` | The evidence framework that governs every claim in the manual. |
| `MATURITY.md` | The maturity model for ideas and capabilities. |
| `ROADMAP.md` | Planned work and evidence targets. |

## Using This Reference

- **Engineers building AI systems:** Start with [manual/30-operations/](manual/30-operations/).
- **Architects making tradeoffs:** Start with [manual/40-architecture/](manual/40-architecture/).
- **Hiring managers evaluating senior engineers:** Start with [manual/70-engineering-reviews/](manual/70-engineering-reviews/).
- **Researchers:** Start with [manual/80-research/](manual/80-research/).

## Dashboard: Validated Knowledge

| Metric | Current | Target |
|--------|---------|--------|
| Hypotheses (E0–E1) | 3 | ≤ 20 |
| Benchmarked claims (E3) | 13 | Increasing |
| Production case studies (E4) | 6 | Increasing |
| Independent reproductions (E5) | 0 | Increasing |
| Retired or superseded claims | 1 | Increasing |

*We optimize for validated knowledge, not volume.*

**Evidence is graded on three axes:**
- **Evidence Level** (E0–E5): How much empirical support exists?
- **Confidence** (Low/Medium/High): How strongly do we currently believe it?
- **Scope** (Narrow/Moderate/Broad): How broadly do we think it applies?

Claims can be downgraded as well as upgraded. Public downgrades are a feature, not a bug.

## Version

**The Human Stack v0.9** — July 2026

See [ROADMAP.md](ROADMAP.md) for planned revisions.

## Related Projects

- **[animus](https://github.com/AreteDriver/animus)** — Reference implementation: personal AI operating environment with evidence-graded maturity
- **[ai-spend](https://github.com/AreteDriver/ai-spend)** — Cost observability as a concrete example of the methodology in practice
- **[ai-skills](https://github.com/AreteDriver/ai-skills)** — Operationalized engineering practices as Claude Code skills

---

*Last updated: 2026-07-12*
