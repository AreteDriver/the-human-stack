# Field Note #005: Stale Module Reference After Reload

## Evidence

**Level:** E3
**Confidence:** Medium
**Scope:** Narrow
**Status:** Stable
**Version:** 1.0
**Last Review:** 2026-07-12
**Review Trigger:** Second occurrence of stale reference after module reload in different codebase
**Expected Upgrade:** E4
**Owner:** @AreteDriver

---

## Observation

`importlib.reload("api.main")` creates a new `app` object, but module-level imports in other test files hold stale references to the old object. Dependency injection overrides silently leak — they apply to the old object while the test client uses the new one.

## Context

In BenchGoblins (June 2026), a test file reloaded `api.main` to test lifespan events. Another test file imported `app` at module level (`from api.main import app`). When the second test set `app.dependency_overrides[get_db_service] = lambda: mock_db`, the override landed on the stale `app`, while `TestClient` was using the new `app`. The result: the route received the real database service instead of the mock.

## Symptom

`RuntimeError: Database not configured or connection failed.` — only when running the full suite, never in isolation.

This is the hallmark of stale reference bugs: they appear in integration, not isolation. The individual test passes. The suite fails.

## The Mechanism

```python
# test_file_a.py
import importlib
import api.main
importlib.reload(api.main)  # Creates NEW module object, binds to sys.modules
# Old module object still exists if referenced elsewhere

# test_file_b.py
from api.main import app  # Captures REFERENCE to old module's app at import time
# ... later ...
app.dependency_overrides[get_db_service] = lambda: mock_db  # Mutates OLD app
# TestClient uses NEW app from reloaded module — override has no effect
```

## Fix

1. **Remove module-level imports of mutable singletons in test files.** Import fresh inside each test method that mutates state.
2. **Clean up overrides after each test.** `app.dependency_overrides.pop(get_db_service, None)`
3. **Avoid module reload in test suites.** If lifespan testing is required, use a separate test module that does not share imports with DI-mutation tests.

## Generalization

This pattern applies beyond FastAPI:

| Domain | Stale Reference | Symptom |
|---|---|---|
| FastAPI | `app` singleton after reload | DI override leaks |
| Django | `settings` module after override | Config changes not reflected |
| Flask | `app` context after teardown | Request context pollution |
| Any framework | Registry/singleton after reload | Plugin/state leakage |

## Implication

Module-level imports of mutable singletons create invisible coupling between test files. The coupling is not in the code — it is in the import graph. Test isolation requires not just database transactions and temp directories, but import-time isolation.

## Open Questions

- Can a linter detect module-level imports of mutable singletons in test files?
- Should test frameworks provide automatic import-time isolation?
- Does this pattern apply to production code (e.g., hot-reloading in dev servers)?

## References

- [ER-001: Mind-Class Fork](../../70-engineering-reviews/ER-001-mind-class-fork.md) — Related divergence pattern
- BenchGoblins fix: `tests/test_matchup_preview.py`, `tests/test_multi_league.py`

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-07-12 | Initial field note with mechanism, symptom, fix, and generalization to other frameworks |
