# Content Pipeline Operationalization

## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** New platform or content format added
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Problem

Engineering work generates artifacts (commits, ADLs, benchmarks, architecture decisions) that contain valuable insights. Without a pipeline, these insights are lost: they sit in repos, never reaching an audience. With an ad-hoc pipeline, the result is inconsistent: some weeks produce three posts, others produce none; voice drifts between platforms; claims lack evidence links.

## Context

In July 2026, the portfolio had 30+ active projects with continuous engineering output. The goal was not to "create content" but to **document work already being done** and distribute it across platforms where it could compound trust and attention.

Positioning: *Building trustworthy AI systems through engineering discipline, not hype.*

## Solution

A structured pipeline with weekly cadence, platform-specific roles, cross-platform repurposing, and evidence-based constraints.

## Platform Roles

| Platform | Role | Content Type | Voice | Success Metric |
|----------|------|--------------|-------|----------------|
| **GitHub** | Proof | READMEs, ADRs, benchmarks, test suites | Crisp, deterministic, no fluff | Stars, forks, external citations |
| **Substack** | Trust | Systems thinking, architecture rationale, lessons | Operational, evidence-backed, 500–1,500 words | Subscriber growth, reply quality |
| **LinkedIn** | Attention | Engineering logs, screenshots, short lessons | Technical, humble, evidence-first | Reach, meaningful engagement |
| **Website** | Hub | Project directory, live demos, contact | Functional, clean, no sales language | Conversion to GitHub/Substack |

**The Funnel:**

```text
LinkedIn (attention)
      ↓
Substack (trust)
      ↓
GitHub (proof)
      ↓
Demo / Newsletter signup
      ↓
Speaking / interviews / opportunities
```

## Weekly Cadence

| Day | Series | Platform | Source Material | Time to Produce |
|-----|--------|----------|-----------------|-----------------|
| **Monday** | Engineering Log | LinkedIn + Substack (short) | Weekend commits, test results, CI fixes | 15 min |
| **Wednesday** | Systems Thinking | Substack | ADL entries, architecture decisions, eval findings | 45–60 min |
| **Friday** | Animus Research Notes | Substack | Research from the week: failures, tradeoffs, benchmarks | 30–45 min |

### Engineering Log Rules

- One screenshot (test suite, CI green, commit graph, architecture diagram)
- One sentence: what shipped
- One sentence: what broke or surprised you
- Max 150 words on LinkedIn; expand to 300 on Substack if warranted

### Systems Thinking Rules

- Start with a problem, not a thesis
- Include at least one concrete example from your own codebase
- End with an actionable takeaway or a question
- No predictions without accompanying evidence
- Length: 500–1,000 words (shorter is better)

### Research Notes Rules

- Document failures openly — "This benchmark surprised us because..."
- Include actual numbers, not hand-wavy trends
- If referencing a model, include the version and quantization
- If referencing architecture, include a link to the ADR or code
- Length: 300–800 words

## Cross-Platform Repurposing

Every piece of source material should yield **at least 3 platform outputs**:

```text
Source: A shipped feature, ADL entry, benchmark run, or research finding
  │
  ├─→ LinkedIn: Engineering Log (short, screenshot, one lesson)
  │
  ├─→ Substack: Systems Thinking or Research Note (expanded, contextualized)
  │
  └─→ GitHub: README update, ADR commit, or benchmark file (permanent record)
```

**Time budget:** 90 minutes per week total.

## Content Templates

### Template A: Engineering Log

```markdown
**Shipped:** [One-line description of what merged this week]

**Evidence:** [Screenshot or link to test suite / CI / commit]

**Lesson:** [One thing that broke, surprised, or taught]

**Context:** [2-3 sentences linking to broader system or prior work]
```

**Example:**

> **Shipped:** Animus Head Phase 5 — natural language intent parser + planner.
>
> **Evidence:** 72/72 head tests green, 179/179 kernel suite green.
>
> **Lesson:** Regex-based intent parsing is sufficient for 20+ patterns at this scale. The risk was over-engineering with an LLM-based parser that would add latency and variance. Deterministic first, intelligent second.
>
> **Context:** This closes the 5-phase Head roadmap (ADL-20260705-001). Next: Phase 6 — multi-turn planning with checkpoint resume.

### Template B: Systems Thinking

```markdown
# [Title]: [Subtitle]

## The Problem
[Describe a real operational pain point.]

## What Most People Do
[The common but flawed approach. Be fair, not snide.]

## What We Tried
[Your actual approach, with specifics from your codebase.]

## The Evidence
[Numbers, test results, benchmark comparisons, architecture diagrams.]

## The Takeaway
[One actionable principle.]
```

### Template C: Research Note

```markdown
# Research Note: [Specific Topic]

**Date:** [YYYY-MM-DD]
**Status:** [Draft / Review / Published]
**Related ADL:** [ADL-YYYYMMDD-###]

## Question
[One sentence.]

## Method
[How you tested or explored it.]

## Results
[What happened. Include numbers.]

## Surprises
[What you didn't expect. This is the value.]

## Next Steps
[What you'll do with this, or what remains unknown.]
```

## Automation & Tooling

### 1. Source Material Discovery (Automated)

Weekly script scanning work for content candidates:

```bash
# ~/projects/notes/scripts/content-audit-weekly.sh
echo "=== Content Candidates for Week of $(date +%Y-%m-%d) ==="
echo "## New ADL Entries:"
git -C ~/projects/animus log --since="7 days ago" --oneline --grep="ADL-"
echo "## Recent Eval Runs:"
find ~/projects/animus -name "*.eval.json" -mtime -7 | head -5
echo "## Recent Benchmarks:"
find ~/projects/animus -name "*benchmark*" -mtime -7 | head -5
```

Produces a raw feed reviewed Monday morning for 5 minutes.

### 2. Content Drafting (Semi-Automated)

Structured prompt for AI assistance:

```markdown
SOURCE: [Paste ADL entry, commit message, or benchmark result]
TASK: Draft an Engineering Log + Systems Thinking post from this source.
RULES:
- Engineering Log: max 150 words, one screenshot, one lesson.
- Systems Thinking: 500–800 words, start with problem, include evidence, end with takeaway.
- Voice: operational, humble, evidence-first. No hype.
- Ban these words: "revolutionary," "game-changing," "the future of AI," "unlock."
```

### 3. Publishing Queue

Track upcoming content:

```markdown
## Content Queue — Week of 2026-07-07
- [ ] Mon: Engineering Log — Animus KC #4 stretch items complete
- [ ] Wed: Systems Thinking — "Why eval calibration matters more than model selection"
- [ ] Fri: Research Note — Local AI eval results: phi4 vs qwen2.5-coder
```

### 4. Cross-Platform Linking

- **LinkedIn** → links to Substack for full article
- **Substack** → links to GitHub repo / ADL / benchmark files as proof
- **GitHub README** → links to Substack for architecture rationale
- **Website** → links to all three, but does not duplicate content

## Banned Words List

| Banned | Replacement | Rationale |
|--------|-------------|-----------|
| "Sovereign" (unqualified) | "local-first," "self-hosted," "auditable" | Technical specificity |
| "Exocortex" | "personal AI operating environment" | Describes function |
| "Cognitive sovereignty" | "control over your data and models" | Plain language |
| "Qliphothic" | [keep in private notes only] | Esoteric, undermines credibility |
| "Revolutionary" | "effective," "measured," "proven" | Evidence replaces hyperbole |
| "Game-changing" | "ship reliably," "reduce waste," "measurable improvement" | Operational outcomes |
| "Unlock" | "enable," "implement," "ship" | Active engineering verbs |
| "The future of AI" | "what we learned from [specific experiment]" | Ground in evidence |
| "Agentic" (vague) | "autonomous workflow," "self-correcting pipeline" | Specific capability |
| "Intelligence" (unqualified) | "routing," "classification," "coordination" | What it actually does |

**Exception:** Technical papers, internal RFCs, and research notes may use speculative vocabulary if defined immediately. Public READMEs, LinkedIn posts, and Substack articles may not.

## Measurement & Review

### Monthly Metrics

| Metric | Target | Source |
|--------|--------|--------|
| LinkedIn posts published | ≥4 | Manual count |
| Substack posts published | ≥12 (3/week) | Substack stats |
| GitHub READMEs updated | ≥2 | GitHub commit log |
| New ADL entries | ≥2 | `decisions/` directory |
| Evidence-based posts / total posts | 100% | Manual review |
| Banned-word violations | 0 | `grep` against published posts |

### Quarterly Brand Review

1. **Property audit:** Re-run the public-properties audit checklist
2. **Voice check:** Review last 10 posts from each platform. Same person?
3. **Funnel check:** Click through LinkedIn → Substack → GitHub. Each step reinforces the next?
4. **Evidence check:** Are claims backed by links to repos, benchmarks, or ADL entries?
5. **Niche check:** Search "Toyota Production System AI" or "Lean AI operations" — are you appearing?

## Evidence

Pipeline operationalized July 2026. First 30-day action plan drafted and tracked in `TODO.md`. Source material discovery script created. Templates stored in `notes/topics/brand-positioning/templates/`.

### Limitations

- **Single-operator:** Pipeline designed for one person. Multi-author workflows not yet defined.
- **No automated scheduling:** Publishing is manual. No CMS or scheduled post integration.
- **Platform dependency:** Substack and LinkedIn are third-party platforms with changing APIs and policies.

## Tradeoffs

- **Optimized for:** Converting engineering work into trust and attention with minimal overhead.
- **Sacrificed:** Content creativity. The pipeline documents work, it does not invent topics.
- **Risk:** Self-referential loop. All content comes from the same source, creating echo chamber. Mitigation: Occasional external references, interviews, or guest contributions.

## Failure Modes

1. **Queue abandonment:** If queue drops below 2 items, pipeline stalls. Mitigation: Weekly script generates candidates; Animus prompts review when queue is low.
2. **Platform drift:** Voice or format drifts between platforms over time. Mitigation: Quarterly brand review with explicit checklist.
3. **Evidence inflation:** Claims grow stronger than the underlying evidence. Mitigation: Every post must link to source material; no standalone claims.

## Future Work

- **E4 target:** Automate LinkedIn/Substack publishing via API (with human approval gate).
- **E4 target:** Measure funnel conversion rates (LinkedIn → Substack → GitHub).
- **E5 target:** Independent reproduction — another engineer applies same pipeline to their portfolio and reports results.

## Citation

```bibtex
@misc{humanstack-ops-content-2026,
  title = {Content Pipeline Operationalization},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E3},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/30-operations/content-pipeline.md}
}
```

## References

- [80-Percent Criteria by Archetype](eighty-percent-criteria.md)
- [Eval Checkpoint Protocol](eval-checkpoint-protocol.md)
- [Benchmark Methodology](../20-evaluation/benchmark-methodology.md)
- Brand positioning audit: `notes/topics/brand-positioning/public-properties-audit-2026-07-06.md`
- Content templates: `notes/topics/brand-positioning/templates/`

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial pipeline with weekly cadence, platform roles, templates, banned words, and measurement framework |
