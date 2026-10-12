# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**yenscastro**


**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-6117965460

Following the repro I posted above, here's my plan for the fix.

**Diagnosis:** the DB probe in `api/routes/health.py` calls `db.execute("SELECT 1")` with a raw string. SQLAlchemy 2.x requires `text("SELECT 1")` — without the wrapper, the call raises `ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')`.

**Scope:** one line change in `api/routes/health.py` to wrap the SQL string, plus a regression test in `tests/unit/test_health.py`. I'll also narrow the pyproject.toml mypy override for `api.routes.health` — the `call-overload` suppression becomes unnecessary after this change. I'm not touching the Redis health check (`settings.redis_host`), which is a separate bug (#62).

**Approach:** import `text` from sqlalchemy, wrap the probe argument, add a test that asserts `/health` reports `postgres: healthy`.

**Test plan:** re-run my repro (`curl /health`) after the fix and confirm `postgres: healthy` (the overall status stays 503 until #62 is fixed), plus the pytest regression test. I'll share before/after output when I have it.

**Unknowns:** I'm not sure yet whether the test suite runs against a real Postgres or mocks the session — I'll check `tests/` before writing the test and adjust if needed.

---

## Your branch

**fix/61-sqlalchemy-text-wrapper**


**Evidence**

**Before** (from my Unit 2 repro comment, https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-5906185951):

$ curl.exe -i http://127.0.0.1:8000/health
HTTP/1.1 503 Service Unavailable
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-30T06:47:34.588420"}}

Server log:
postgres_health_check_failed error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=4e6a756e-ded7-48f9-aaf7-5687a8483a46

**After** (post-fix, running the app from `main` + my branch):

$ curl.exe -i http://127.0.0.1:8000/health
HTTP/1.1 503 Service Unavailable
{"detail":{"status":"unhealthy","dependencies":{"postgres":"healthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"..."}}

postgres flips from "unhealthy" to "healthy". Overall status remains 503 because the Redis check fails (#62) — as predicted in the plan's test plan section.

**Regression test:**

$ python -m pytest tests/unit/test_health.py -v
================================================= test session starts =================================================
platform win32 -- Python 3.12.10, pytest-9.1.1, pluggy-1.6.0 -- ...\.venv\Scripts\python.exe
rootdir: C:\Users\yens5\Desktop\ai301-unit1-starter-main\pathreview-ai301-fa26-s3
configfile: pyproject.toml
collected 1 item

tests/unit/test_health.py::test_health_reports_postgres_healthy PASSED                                           [100%]

============================================ 1 passed, 4 warnings in 4.37s ============================================

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke test with --limit 3: 3/3 agreement. pkg-01 (wrong-cause) reject, pkg-02 (clear-accept) accept, pkg-03 (clear-accept) accept.

2. Canary check on unseen categories with --only pkg-05,pkg-09,pkg-18: 3/3 agreement. pkg-05 (clear-accept) accept, pkg-09 (clear-accept) accept, pkg-18 (unbuildable) reject.

3. First full run: 19/20. Disagreement on pkg-14 (clear-accept, gold accept, mine reject on executable_by_stranger).

4. Full run with --save-run eval-run.txt: 19/20. Categories: clear-accept 6/7, scope-creep 4/4, thread-convention 2/2, unbuildable 3/3, wrong-cause 4/4.

The last score (19/20) matches the agreement line in the committed eval-run.txt.

**Package analysis**

pkg-14 (zellij-org/zellij#5174). My rubric said reject; the gold label said accept. The disagreement came from `executable_by_stranger`, which requires the plan to name the exact file(s) and function(s) to change. The plan named the module-level target but not the specific file path, so my check failed it. Gold treated module-level naming as sufficient — a stranger who knows the repo could still find the code from the plan's description. I concluded my check reads stricter than the gold threshold and left it as-is rather than loosening, because loosening risked flipping the unbuildable-category rejects (pkg-10, pkg-17, pkg-18), whose plans I do want to reject. The tension is exactly what a rubric surfaces: naming a path is concrete evidence, but "the module plus the function name" is often enough in practice. The gold set chose the looser read; I chose the stricter one and accepted the one-package miss.

**Check rationale**

My `executable_by_stranger` check reads, verbatim:

> "The plan's files-to-touch list and approach section, read as if you were a contributor who has never seen the codebase. | The plan names the file(s) and function(s) to change, or names the module and the specific function within it. Either is sufficient if a stranger could open the repo and find the code. A plan that says 'investigate the health module' with no function or path fails."

I wrote it this way because the assignment's failure families include "a stranger could not start executing it." The bar I chose — file path OR module plus function — is slightly stricter than the gold set's read on pkg-14, where a module-level naming was accepted. I kept the stricter version because the unbuildable category (pkg-10, pkg-17, pkg-18 — all gold rejects) hinges on plans whose files-to-touch are too vague to start from, and I'd rather miss one arguable accept than lose a whole category of real rejects.

**Trade-offs**

The stricter `executable_by_stranger` threshold costs me pkg-14. A plan that names the module and describes the change at the module level, without pasting the exact file path, fails the check. Gold accepts that as sufficient. I traded the one-package miss for sharper rejection on the unbuildable category: the three unbuildable packages (pkg-10, pkg-17, pkg-18) each fail this check for real reasons — their plans name behaviors to "investigate" rather than specific files and functions. Loosening the threshold would risk flipping one or more of those to accept, breaking the unbuildable category match. One arguable miss beats three clear ones. If I revisit, I'd add a preferred check that distinguishes "module named with a function" from "module named alone" and downgrade the required threshold to the latter, keeping the strictness in the ranking instead of the verdict.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
