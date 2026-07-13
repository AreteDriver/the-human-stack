# Overview: Reading The Human Stack

## Evidence

**Level:** E1
**Confidence:** High
**Scope:** Broad
**Status:** Draft
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** First external review citing accessibility gap
**Expected Upgrade:** E2
**Owner:** @AreteDriver

---

## The Short Version

If you are building or operating AI systems in production, this manual is for you. It treats AI deployment as an engineering discipline — not a prompt-crafting exercise or a model-selection problem.

The Human Stack is organized around one idea: **AI systems should be engineered as operational systems.** That means designing for reliability under uncertainty, measuring continuously, governing with policies that evolve, and improving through feedback loops rather than one-time releases.

## Why This Exists

Most AI engineering knowledge lives in one of three places:

1. **Private company playbooks** — inaccessible, tribal, lost when people leave.
2. **Academic papers** — rigorous but disconnected from production constraints.
3. **Blog posts and tweets** — timely, but unversioned, unbenchmarked, and often wrong six months later.

There is no public, living reference that treats AI deployment with the same rigor as distributed systems, reliability engineering, or manufacturing operations. This manual tries to fill that gap.

## What Makes This Different

### Every Claim is Graded

At the top of every chapter, you will see an **Evidence block**:

```
Level: E3
Confidence: Medium
Scope: Moderate
```

This tells you how much empirical support exists, how strongly we believe it, and how broadly we think it applies. **E1** means "design heuristic" — useful, but not proven. **E4** means "production case study with metrics." **E5** means "independently reproduced by another team."

Claims can be downgraded as well as upgraded. Public downgrades are a feature, not a bug.

### Failed Experiments Are First-Class Content

Most references hide their failures. This one publishes them. See `manual/60-failed-experiments/` for detailed postmortems on systems that were built, tested, and abandoned — with full reasoning.

### It Is a Graph, Not a Book

Do not read cover-to-cover. Follow the path that matches your role and intent. Every chapter links to related chapters. The manual is designed to be returned to repeatedly, not consumed once.

## What "Operational Systems" Means

AI systems differ from traditional software in three ways:

| Dimension | Traditional Software | AI Systems |
|---|---|---|
| **Correctness** | Deterministic — a function returns the same output for the same input | Probabilistic — outputs vary; correctness is a distribution |
| **Failure** | Binary — it works or it crashes | Gradual — accuracy degrades, drift accumulates, biases emerge |
| **Specification** | Requirements document defines behavior | Behavior emerges from model, data, and context; no complete spec exists |

These differences mean AI systems require different engineering practices:

- **Continuous evaluation**, not terminal testing
- **Governance as architecture**, not compliance checkbox
- **Failure as data**, not bug to fix once
- **Human judgment as anchor**, not replaced by automation

These practices are not new. Manufacturing operations (Lean, Toyota Production System), site reliability engineering (SRE), and human factors engineering have used them for decades. The Human Stack applies them to AI systems.

## How This Manual Is Organized

| Section | What You Will Find | Start Here If... |
|---|---|---|
| `00-introduction/` | What this is and how to read it | You are new to this reference |
| `10-case-studies/` | Real systems, real metrics, real tradeoffs | You learn from concrete examples |
| `20-evaluation/` | How to evaluate models, agents, and deployments | You need to justify decisions with data |
| `30-operations/` | Runbooks, playbooks, operational patterns | You run systems in production |
| `40-architecture/` | Proven patterns and their failure modes | You design systems from scratch |
| `50-principles/` | The design philosophy and core ideas | You want to understand the "why" |
| `60-failed-experiments/` | Postmortems and abandoned designs | You want to learn from mistakes |
| `70-engineering-reviews/` | Retrospectives with reversed decisions | You evaluate senior engineers |
| `80-research/` | Field notes and observations in progress | You are exploring the problem space |

## How to Start Reading

### "I need to make a decision today."

1. Read the **Evidence block** at the top of any chapter first. It tells you whether the claim is proven, hypothesized, or somewhere in between.
2. Check the **Tradeoffs** section. Every chapter includes what was optimized for and what was sacrificed.
3. Follow **References** to related chapters for context.

### "I am evaluating whether to trust this manual."

1. Read `EVIDENCE.md` for the grading framework.
2. Read `50-principles/human-stack.md` for the core philosophy.
3. Pick one case study (`10-case-studies/`) and one engineering review (`70-engineering-reviews/`). Check whether the evidence matches the claims.

### "I want to apply these ideas to my system."

1. Start with `30-operations/` for operational patterns.
2. Read `40-architecture/` for design patterns.
3. Check `20-evaluation/` for measurement methodology.
4. Reference implementations (Animus, drift-monitor, memboot) are linked from `references/`. They are evidence, not the product.

## Understanding the Evidence Framework

The Human Stack uses three axes to grade every claim:

### Evidence Level (E0–E5)

| Level | Meaning |
|---|---|
| **E0** | Hypothesis — no evidence yet |
| **E1** | Design heuristic — reasoning, no data |
| **E2** | Anecdotal — observed once, not reproduced |
| **E3** | Benchmarked — measured under controlled conditions |
| **E4** | Production case study — deployed, observed, metrics collected |
| **E5** | Independent reproduction — validated by another team |

### Confidence (Low / Medium / High)

How strongly do we currently believe the claim, separate from how much evidence exists? A claim can have strong evidence but low confidence if the evidence is narrow or contested.

### Scope (Narrow / Moderate / Broad)

How broadly do we think the claim applies? A pattern validated on a single system (Narrow) may still be useful. A principle claimed to apply to all agentic systems (Broad) requires more evidence.

## A Note on Language

This manual uses operational language, not AI hype. Words like "revolutionary," "game-changing," and "unlock" do not appear. Words like "measured," "tested," and "validated" do. The [banned words list](../30-operations/content-pipeline.md#banned-words-list) explains why.

## Contributing

This is a living reference. Corrections, additions, and evidence upgrades are welcome. Editorial standards are defined in `CONSTITUTION.md`. If you have a case study, benchmark, or failed experiment that fits the framework, open a pull request.

## Citation

```bibtex
@misc{humanstack-overview-2026,
  title = {Overview: Reading The Human Stack},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E1},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/00-introduction/overview.md}
}
```

## References

- [VISION.md](../../VISION.md) — Mission, non-goals, principles
- [CONSTITUTION.md](../../CONSTITUTION.md) — Editorial rules
- [EVIDENCE.md](../../EVIDENCE.md) — Evidence grading framework
- [The Laws of Operational AI](laws.md) — Core design heuristics
- [The Human Stack Philosophy](../50-principles/human-stack.md) — Foundational principles
- [Content Pipeline](../30-operations/content-pipeline.md) — Banned words and voice guidelines

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial overview addressing accessibility gap identified in external review |
