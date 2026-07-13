# Local Model Benchmarks: Calibration Run 2026-07-06

## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Moderate
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** New model release or hardware change
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Problem

Selecting models for local inference is typically done by leaderboard position or community consensus, not by task-specific measurement. This produces mismatches: a model ranked #1 on general benchmarks may be slower, less precise, or inappropriate for code generation or architecture design.

## Context

In June 2026, the portfolio required a local inference stack (AMD RX 7900 XTX + Ollama) to power pre-build workflows for 30+ projects. Four model roles were needed:

- **General reasoning** — everyday queries, context assembly
- **Code generation** — structured edits, AST-aware transformations
- **Architecture design** — system design, precision, evidence quality
- **Vision** — image understanding, multimodal prompts

Constraints:
- **Zero cloud cost** — all evaluation local
- **Multi-suite coverage** — code edits, architecture precision, personal quality
- **Continuous recalibration** — weekly re-evaluation as models update

## Methodology

See [Benchmark Methodology](benchmark-methodology.md) for the full framework. This chapter reports the July 6, 2026 calibration run.

### Conditions

- **Hardware:** AMD RX 7900 XTX
- **Inference:** Ollama local stack
- **Judge:** qwen2.5:32b (discriminating judge)
- **Repetitions:** 3 per task
- **Temperature:** 0.0
- **Max tokens:** 4096

### Models Evaluated

| Model | Size | Role tested |
|---|---|---|
| llama3.1:8b | 8B | General / vision |
| qwen2.5-coder:14b | 14B | Code generation |
| phi4:14b | 14B | Architecture / reasoning |
| qwen2.5:32b | 32B | Judge / reference |

---

## Results

### Summary Table

| Model | Code Gen | Architecture | Vision | Overall |
|---|---|---|---|---|
| **phi4:14b** | **0.948** | **0.932** | 0.888 | **0.923** |
| qwen2.5:32b | 0.928 | 0.931 | 0.875 | 0.911 |
| qwen2.5-coder:14b | 0.928 | 0.920 | **0.903** | 0.917 |
| llama3.1:8b | 0.894 | 0.900 | 0.894 | 0.896 |

All models **PASS** threshold (0.70) on all suites.

---

### Suite 1: Code Generation (Rubric: code-edit)

| Model | Composite | Latency (avg) | Notes |
|---|---|---|---|
| phi4:14b | **0.948** | ~15.5s | Leads on `completeness` (0.90) and `efficiency` (0.97) |
| qwen2.5:32b | 0.928 | ~33.6s | Same correctness as phi4, docked on completeness |
| qwen2.5-coder:14b | 0.928 | ~14.5s | Best speed/quality ratio; same score as 32b, **2.3× faster** |
| llama3.1:8b | 0.894 | ~8.8s | Fastest; weakness in `completeness` (0.82) |

**Key insight:** `qwen2.5-coder:14b` achieves the same composite as `qwen2.5:32b` at **2.3× lower latency** — the 14B coder variant is the sweet spot for code tasks. phi4:14b is the quality ceiling but slower.

---

### Suite 2: Architecture Precision (Rubric: personal-quality)

| Model | Composite | Latency (avg) | Notes |
|---|---|---|---|
| phi4:14b | **0.932** | ~22.3s | Leads on `precision` (0.94) and `actionability` (0.87) |
| qwen2.5:32b | 0.931 | ~47.1s | Excellent `relevance` (1.0); slowest |
| qwen2.5-coder:14b | 0.920 | ~18.4s | Best speed/quality; perfect `relevance` and `hallucination_safety` |
| llama3.1:8b | 0.900 | ~10.0s | Fastest; `actionability` (0.82) is the gap vs leaders |

**Key insight:** Architecture prompts reward precision and evidence quality. All models score 1.0 on `format_compliance` — the structured output training is working.

---

### Suite 3: Vision (Rubric: personal-quality)

| Model | Composite | Latency (avg) | Notes |
|---|---|---|---|
| qwen2.5-coder:14b | **0.903** | ~22.6s | Best overall; leads on `actionability` (0.94) |
| llama3.1:8b | 0.894 | ~10.7s | Fastest; surprisingly competitive on vision |
| phi4:14b | 0.888 | ~18.4s | Evidence quality lower (0.77) vs architecture run |
| qwen2.5:32b | 0.875 | ~36.6s | Weakest `actionability` (0.83); not worth 32b for vision |

**Key insight:** Vision prompts are **shorter and less structured** than architecture prompts. The 32B model does not benefit from extra capacity here — smaller models perform equally well or better. The `evidence_quality` dimension is the main differentiator (0.75–0.80 across all models), suggesting the rubric expects more detailed justification than these models provide.

---

## Comparison to June 22 Baseline

| Model | Code Gen Δ | Arch Δ | Vision Δ | Trend |
|---|---|---|---|---|
| phi4:14b | +0.008 | +0.032 | +0.038 | ↑ Improving across all suites |
| qwen2.5-coder:14b | +0.020 | +0.015 | +0.013 | ↑ Consistent improvement |
| qwen2.5:32b | +0.008 | +0.006 | −0.005 | → Stable (vision slightly down) |
| llama3.1:8b | +0.024 | +0.015 | +0.012 | ↑ Largest improvement margin |

**Notable:** All models improved on code-generation and architecture since June 22. The improvement is likely due to rubric/judge calibration drift (the judge may be scoring slightly more leniently now) rather than model updates. The `evidence_quality` dimension remains the hardest to score well on.

---

## Recommendations

| Task | Recommended Model | Rationale |
|---|---|---|
| **Default code tasks** | qwen2.5-coder:14b | Same quality as 32b, 2.3× faster |
| **Architecture / design tasks** | phi4:14b | Highest precision, acceptable latency (~22s) |
| **Vision / prompt tasks** | llama3.1:8b | Fastest, competitive score. Vision does not need large models |
| **Judge role** | qwen2.5:32b | Discriminating enough to detect real differences |

**Evidence quality gap:** Consider adjusting the rubric or adding a "chain-of-thought" requirement to vision prompts. All models score 0.75–0.80 on `evidence_quality` — this is the dimension with the most headroom.

---

## Evidence

### Benchmark Files

- `code-generation-20260706-220137.json`
- `prebuild-architecture-20260706-222100.json`
- `prebuild-vision-20260706-224248.json`

### Quality Metrics

| Metric | Target | Actual |
|---|---|---|
| All models pass threshold | 100% | 100% (4/4) |
| Score variance (3 repetitions) | <5% | <3% (stable) |
| Judge consistency | >95% | ~97% (estimated) |

### Limitations

- **Local-only:** Results may not transfer to cloud inference (different latency characteristics, no rate limiting).
- **Single GPU:** Multi-GPU scaling not evaluated.
- **English-only:** No evaluation on multilingual tasks.
- **Evidence quality headroom:** 0.78 is good; 0.90+ would be required for high-stakes deployments.
- **Calibration drift:** Score increases may reflect judge leniency, not model improvement.

---

## Tradeoffs

- **Optimized for:** Task-specific model selection over leaderboard position.
- **Sacrificed:** Generality. A model strong on code may be weaker on vision. No single "best" model exists.
- **Risk:** Overfitting to rubrics. Models may optimize for our scoring dimensions without improving real-world performance.

## Failure Modes

1. **Benchmark decay:** As models update, baselines become stale. Mitigation: Weekly recalibration with version pinning.
2. **Rubric drift:** Judge model updates may shift scoring. Mitigation: Track judge version and calibration markers.
3. **Suite bias:** Tasks may favor certain architectures. Mitigation: Diverse task sources and regular suite review.

## Future Work

- **E4 target:** Validate same methodology against cloud inference (OpenAI, Anthropic APIs) with identical suites.
- **E4 target:** Correlate benchmark scores with production outcomes (adoption rates, error reports).
- **E5 target:** Independent reproduction by another team with identical hardware and methodology.

## Citation

```bibtex
@misc{humanstack-eval-local-2026,
  title = {Local Model Benchmarks: Calibration Run 2026-07-06},
  author = {Young, James C.},
  year = {2026},
  month = {July},
  version = {1.0},
  evidence = {E3},
  howpublished = {The Human Stack},
  url = {https://github.com/AreteDriver/the-human-stack/blob/main/manual/20-evaluation/local-model-benchmarks.md}
}
```

## References

- [Benchmark Methodology](benchmark-methodology.md)
- [CS-001: Animus P5 Discovery](../10-case-studies/CS-001-animus-p5-discovery.md)
- [CS-004: Session Controller](../10-case-studies/CS-004-session-controller.md)
- Animus eval calibration memory: `~/.claude/projects/-home-arete/memory/local-ai-eval-calibration-20260706.md`

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial calibration results with per-suite breakdown, recommendations, and comparison to June baseline |
