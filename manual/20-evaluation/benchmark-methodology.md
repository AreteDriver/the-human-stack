# Benchmark Methodology for AI System Evaluation

## Evidence

**Level:** E3
**Confidence:** Medium
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-07
**Owner:** @AreteDriver

---

## Problem

AI systems are evaluated inconsistently. Benchmarks are often:

- **Cherry-picked:** Reporting only favorable results
- **Irreproducible:** Missing methodology, hardware, or configuration details
- **Single-dimensional:** Measuring accuracy without latency, cost, or reliability
- **Static:** Run once at release, never repeated as models drift

This creates a trust problem. Without a repeatable, multi-dimensional evaluation methodology, teams cannot compare models meaningfully, detect regressions, or justify architecture decisions.

## Context

In June 2026, we faced a specific version of this problem: selecting models for a local inference stack (AMD RX 7900 XTX + Ollama) that would power pre-build workflows for 30+ projects. The constraints were:

- **Zero cloud cost:** All evaluation must run locally
- **Four model tiers:** General reasoning, code generation, architecture design, vision
- **Multi-suite coverage:** Code edits, architecture precision, personal quality scoring
- **Continuous recalibration:** Weekly re-evaluation as models update

## Methodology

### 1. Define Evaluation Dimensions

We evaluate across four dimensions, not one:

| Dimension | Question | Measurement |
|-----------|----------|-------------|
| **Correctness** | Does the output solve the problem? | Pass/fail against reference solution + expert review |
| **Precision** | Is the output free from hallucination? | Factual accuracy check against source material |
| **Efficiency** | What does it cost in time and resources? | Latency, token count, memory usage |
| **Evidence Quality** | Does the output cite and justify its reasoning? | Rubric-based scoring (0–1) |

Evidence quality is the dimension most teams ignore. It measures whether the model's output includes verifiable reasoning, not just plausible-sounding answers.

### 2. Build Evaluation Suites

A suite is a collection of tasks with known good answers, organized by capability:

| Suite | Tasks | Focus |
|-------|-------|-------|
| **Code Edit** | 50+ code transformation tasks | Correctness of structured edits (AST-aware) |
| **Architecture Precision** | 30+ system design prompts | Accuracy of architectural claims against codebase |
| **Personal Quality** | 40+ personal workflow tasks | Relevance, precision, actionability, hallucination safety |

Each suite includes:
- **Prompt template:** Standardized input with variable injection
- **Reference solution:** Human-validated correct answer
- **Rubric:** Scoring criteria with explicit thresholds
- **Failure taxonomy:** Classification of failure modes (see below)

### 3. Score with Rubrics, Not Vibes

Every task is scored against a defined rubric. Example (Personal Quality, 6 dimensions):

| Dimension | Weight | Threshold |
|-----------|--------|-----------|
| Relevance | 20% | Output directly addresses prompt |
| Precision | 20% | No unnecessary or generic content |
| Actionability | 20% | Specific steps or decisions provided |
| Evidence Quality | 20% | Claims cite verifiable sources |
| Format Compliance | 10% | Matches requested output format |
| Hallucination Safety | 10% | No fabricated facts or references |

Scoring is deterministic: the same output against the same rubric produces the same score.

### 4. Run Multi-Model Comparison

For each suite, execute against all candidate models under identical conditions:

```
Suite: Code Edit
Models: qwen2.5-coder:14b, qwen2.5-coder:32b, phi4:14b, llama3.1:8b
Conditions: RX 7900 XTX, Ollama 0.4.x, temperature=0.0, max_tokens=4096
Repetitions: 3 per task (to detect flakiness)
```

### 5. Apply Failure Taxonomy

Every failure is classified. Two orthogonal classifiers:

**Technical failures:**
- `schema_drift` — Output format deviates from expected structure
- `hallucination` — Fabricated facts or references
- `wrong_answer` — Incorrect solution despite plausible reasoning
- `refused` — Model declined to answer
- `over_budget` — Exceeded token or time limits
- `timeout` — No response within threshold
- `contract_violation` — Broke explicit prompt constraints

**Content failures:**
- `F1_missing_constraint` — Ignored explicit requirement
- `F2_wrong_assumption` — Assumed context not in prompt
- `F3_weak_evidence` — Unsupported claims presented as fact
- `F4_too_generic` — Could apply to any similar problem
- `F5_overlong` — Exceeded scope or verbosity
- `F6_poor_structure` — Difficult to extract actionable information
- `F7_false_precision` — Specific-sounding but unverifiable claims
- `F8_insufficient_adversarial_review` — Did not consider edge cases

### 6. Track Over Time

Benchmarks are not one-time events. We maintain:

- **Baseline:** First run against a model version
- **Delta:** Subsequent runs compared to baseline
- **Drift detection:** Automated flagging when scores drop >5% from baseline

## Evidence

### Calibration Run: 2026-07-06

**Hardware:** AMD RX 7900 XTX, Ollama local inference stack
**Suites:** 3 (Code Edit, Architecture Precision, Personal Quality)
**Models evaluated:** 4

#### Key Findings

| Model | Role | Strength | Evidence |
|-------|------|----------|----------|
| **qwen2.5-coder:14b** | Code generation | Same quality as 32b at 2.3× speed | E3, 50-task suite, 3 repetitions |
| **phi4:14b** | Architecture design | Highest precision on system design prompts | E3, 30-task suite, 3 repetitions |
| **llama3.1:8b** | Vision tasks | Sufficient for image understanding | E3, subset of vision tasks |

#### Aggregate Results

| Dimension | June Baseline | July Result | Delta |
|-----------|---------------|-------------|-------|
| Correctness | 0.72 | 0.78 | +8.3% |
| Precision | 0.75 | 0.81 | +8.0% |
| Efficiency | N/A (first measured) | 14b models optimal | New |
| Evidence Quality | 0.72 | 0.78 | +8.3% |

**All models improved since June 22 baseline.** The improvement suggests either model updates or methodology refinement (confounding variable not yet isolated).

#### Known Limitations

- **Local-only:** Results may not transfer to cloud inference (different latency characteristics, no rate limiting)
- **Single GPU:** Multi-GPU scaling not evaluated
- **English-only:** No evaluation on multilingual tasks
- **Evidence quality is the dimension with most headroom:** 0.78 is good; 0.90+ would be required for high-stakes deployments

### Reproducibility

To reproduce this calibration:

1. Install Ollama with ROCm support (AMD GPU)
2. Pull models: `qwen2.5-coder:14b`, `qwen2.5-coder:32b`, `phi4:14b`, `llama3.1:8b`
3. Clone evaluation suite (reference implementation in drift-monitor)
4. Run: `animus-forge eval run <suite> --rubric personal-quality --prompt-version v1.2`
5. Compare against baseline in `docs/metrics/G1_EVIDENCE_BASELINE.md`

## Tradeoffs

- **Optimized for:** Repeatability and multi-dimensional comparison
- **Sacrificed:** Speed of evaluation. A full calibration across 4 models and 3 suites takes ~6 hours on local hardware.
- **Risk:** Overfitting to benchmarks. Models may optimize for our rubrics without improving real-world performance.

## Failure Modes

1. **Benchmark decay:** As models update, baselines become stale. Mitigation: Weekly recalibration with version pinning.
2. **Rubric drift:** Scorers apply rubrics inconsistently over time. Mitigation: Reference solutions and periodic inter-rater reliability checks.
3. **Suite bias:** Tasks may favor certain model architectures. Mitigation: Diverse task sources and regular suite review.

## Future Work

- **E4 target:** Validate methodology against cloud inference (OpenAI, Anthropic APIs) with identical suites
- **E4 target:** Correlate benchmark scores with production outcomes (adoption rates, error reports)
- **E5 target:** Independent reproduction by another team using published methodology

## References

- [CS-001: Animus P5 Discovery](../10-case-studies/CS-001-animus-p5-discovery.md)
- [drift-monitor reference](../../references/drift-monitor.md)
- Failure taxonomy: `animus/packages/forge/rubrics/` and `animus/packages/forge/failure_taxonomy*.py`

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-07 | Initial methodology and July 2026 calibration results |
