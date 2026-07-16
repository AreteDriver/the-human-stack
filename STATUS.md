# Project Status

| Attribute | Value |
|-----------|-------|
| Status | **OPERATIONAL** |
| Last verified | 2026-07-16 |
| Installable? | N/A — this is a reference, not software |
| Tested? | Evidence-graded claims; 13 benchmarked (E3), 6 production case studies (E4) |
| Documented? | Yes — living manual with editorial constitution |

## What Works

- **Evidence Framework:** Every claim graded E0–E5 with explicit criteria
- **Living Manual:** 4 chapters (Operations, Architecture, Engineering Reviews, Research) with templates
- **Constitution:** Editorial rules that govern revision, retirement, and promotion of claims
- **Dashboard:** Public metrics showing hypothesis → validated knowledge pipeline
- **Templates:** Standard formats for chapters, reviews, benchmarks, postmortems

## What Doesn't Work Yet

- Zero independent reproductions (E5) — the highest evidence grade remains unclaimed
- No automated link checker or stale-reference detection in CI
- Not yet integrated as Animus Knowledge Curator source

## Usage

```bash
git clone https://github.com/AreteDriver/the-human-stack.git
# Read VISION.md → CONSTITUTION.md → manual/START-HERE.md
```

## Relationship to Animus

The Human Stack is the **methodology** that Animus implements. Animus is the engineering system; this repository is the reasoning behind it. Claims in the manual are tested by Animus features (e.g., eval practice, session continuity, evidence baselines).
