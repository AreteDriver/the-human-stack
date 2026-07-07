# Style Guide

This document defines the writing standards for The Human Stack. It applies to all chapters, reviews, benchmarks, and reference material.

## Tone

- **Measured.** Let evidence carry the claim.
- **Specific.** Avoid generics. Name the system, the metric, the failure mode.
- **Honest about uncertainty.** "We don't know" is a valid and valuable statement.
- **Engineering-first.** We are operators, not evangelists.

## Voice Rules

### Prefer

- measured claims
- specific evidence
- tradeoffs and limitations
- observations over assertions
- reproducible results
- precise terminology

### Avoid

| Word/Phrase | Replacement |
|-------------|-------------|
| "revolutionary" | Describe the actual change |
| "game-changing" | Quantify the impact |
| "world-first" | Cite the independent verification, or remove |
| "solves AI" | Specify what problem and under what constraints |
| "best" | Define the evaluation criteria and show the comparison |
| "obviously" | Show the reasoning |
| "just" | Nothing is "just" in production systems |
| "simply" | If it were simple, we wouldn't be writing about it |

## Formatting

### Markdown Conventions

- Use ATX-style headers (`## Header`)
- Soft wrap prose (no hard line limits)
- Code blocks must specify language: ` ```python `, ` ```bash `
- Use `-` for unordered lists, `1.` for ordered
- Relative links for internal references, full URLs for external

### Evidence Block

Every chapter must begin with the evidence block defined in [EVIDENCE.md](EVIDENCE.md).

### Standard Sections

Chapters should use these sections where applicable:

```markdown
## Problem

## Context

## Implementation

## Evidence

## Tradeoffs

## Failure Modes

## Future Work

## References

## Revision History
```

Not all sections apply to every chapter, but "Problem," "Evidence," and "Tradeoffs" are strongly encouraged.

### Tables

Prefer tables for comparisons, benchmarks, and structured data.

### Diagrams

ASCII diagrams are acceptable. If using images, include alt text and source files where possible.

## Naming Conventions

- **Chapters:** `## Title` — sentence case, descriptive
- **Files:** `kebab-case.md`
- **Decision Records:** `ER-NNNN-title.md`
- **Case Studies:** `CS-NNNN-title.md`
- **Benchmarks:** `BM-NNNN-title.md`

## Cross-References

Every chapter must link to related chapters. Use relative paths:

```markdown
See [drift-monitor evaluation](../20-evaluation/drift-monitor-benchmark.md).
```

## Dates

- Use ISO 8601: `2026-07-07`
- Include dates on time-sensitive claims
- Include "verified on" dates for references to external systems

## Numbers

- Use SI units where applicable
- Include error bounds or confidence intervals for measured values
- Prefer exact counts over approximations ("500 prompts" not "hundreds of prompts")
