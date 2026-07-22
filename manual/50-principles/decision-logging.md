# Decision Logging

> **A decision log is operational memory, not a journal.**

Engineering teams lose reasoning. Not the decision itself — that lives in the code — but *why* the decision was made, what options were rejected, and under what conditions the decision should be revisited. Six months later, the same question surfaces, the same options get re-litigated, and the same mistakes recur.

Decision logging prevents this by treating irreversible or high-leverage choices as first-class artifacts. Every entry is append-only (no edits after logging), carries explicit tradeoffs, and defines a revisit condition. The result is compounding judgment: the team gets smarter over time rather than repeatedly solving the same problems.

---

## When to Log

Log only decisions that:

1. **Remove options** — Architecture choices that close doors.
2. **Lock structure** — Scope cuts, UX contracts, API boundaries.
3. **Will matter in 30+ days** — Temporary workarounds need different tracking.

Do not log:
- Bug fixes with obvious root causes.
- Style or formatting choices governed by linters.
- Anything that can be reversed in under an hour without side effects.

---

## Format

Every entry takes ≤ 60 seconds to write. The structure is rigid by design — ambiguity is the enemy:

```
## ADL-YYYYMMDD-###

**Project:** <name>
**Class:** [ARCH | UX | SCOPE | TOOLING | PHIL]

**Decision:**
<One sentence>

**Chosen Option:**
<The selected approach>

**Rejected Options:**
- <Option A>
- <Option B>

**Why:**
- <Reason 1>
- <Reason 2>

**Tradeoffs Accepted:**
- <What you gave up>

**Revisit If:**
- <Condition that invalidates this>
```

### Decision Classes

| Class | Use For |
|-------|---------|
| **ARCH** | Architecture, system design, data models, APIs |
| **UX** | Interactions, controls, user experience |
| **SCOPE** | Cuts, exclusions, prioritization, MVP boundaries |
| **TOOLING** | Languages, frameworks, platforms, build systems |
| **PHIL** | Guiding principles, strategy, posture |

### Naming Convention

`ADL-YYYYMMDD-###`

- `ADL` = Arete Decision Log
- `YYYYMMDD` = Date (e.g., 20260118)
- `###` = Sequential number for that day (001, 002)

---

## Rules

### 1. No edits after logging

Addendums only. If a decision turns out to be wrong, log a new entry with the reversal rationale. The original entry stays — it captures what was believed at the time, which is often as valuable as the corrected decision.

### 2. Every entry must have a "Revisit If"

If you cannot articulate what would make this decision wrong, you do not understand it well enough to log it. The revisit condition is a forcing function for clarity.

### 3. Rejected options must be specific

"We considered other approaches" is worthless. Name the options and why each was rejected. Future readers need to know what was already tried.

### 4. Tradeoffs are mandatory

A decision with no accepted downside is either trivial or delusional. Every entry should make the team slightly uncomfortable.

### 5. Evidence, not authority

The "Why" section should reference observable facts, not roles or seniority. "The lead said so" is not a reason.

---

## Integration with Other Systems

### Evidence Framework

Decisions at E0–E1 (hypothesis) carry low confidence and short revisit windows. Decisions at E3+ (benchmarked or deployed) carry higher confidence and longer revisit windows. See [EVIDENCE.md](../../EVIDENCE.md) for the full grading system.

### Architecture Reviews

Before major architecture changes, require an ADL entry as a precondition. The review evaluates the decision document, not just the code. This shifts review focus from "does it work?" to "is the reasoning sound?"

### Hiring and Interviews

Curated decision logs are more informative than project lists. They show how a candidate thinks under uncertainty, what tradeoffs they accept, and whether they learn from mistakes. See [manual/70-engineering-reviews/](../70-engineering-reviews/) for how to evaluate them.

---

## Example

```markdown
## ADL-20260424-001

**Project:** animus-forge
**Class:** TOOLING

**Decision:**
Temporarily lower test coverage gate from 97% → 95% to unblock Dependabot backlog.

**Chosen Option:**
Lower gate with inline TODO marker and 24-hour restoration check.

**Rejected Options:**
- Hard-grind 342 lines of defensive coverage (3–4 hours, high context-switch cost)
- Keep shipping against a red gate (admin-bypass theater, defeats the purpose)
- Disable gate entirely (no guardrail during the fix window)

**Why:**
- Full suite is 9,800 tests passing; only the percentage gate is red.
- Dependabot backlog includes HIGH-severity xmldom patches.
- A gate that stays red for days is a broken signal.

**Tradeoffs Accepted:**
- Temporary coverage blind spot for 24 hours.
- Requires discipline to restore gate; otherwise debt becomes permanent.

**Revisit If:**
- Gate not restored within 48 hours.
- Coverage drops below 95% even after gate restoration.
```

---

## Storage

- **Monthly files:** `decisions/YYYY-MM.md`
- **Index:** Each monthly file links to entries within that month.
- **Featured subset:** A curated list (8–10 entries) for external communication — hiring, consulting, public talks. This subset is reviewed quarterly. The curation itself is a signal of judgment.

---

*This system adapted from the Toyota Production System principle of *genchi genbutsu* (go see for yourself) — decisions are made by those closest to the problem, and the reasoning is visible to everyone who follows.*
