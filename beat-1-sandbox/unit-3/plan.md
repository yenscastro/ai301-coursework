# Plan: fix #61 — Health check DB probe passes a raw SQL string

## Diagnosis

The health check at `api/routes/health.py` calls `await db.execute("SELECT 1")` with a raw string. Under SQLAlchemy 2.x, `Session.execute` no longer accepts a plain string — it requires a `ClauseElement` (or a `text()` wrapper). The call raises:

```
sqlalchemy.exc.ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
```

I reproduced this on the current `main` of codepath/pathreview-ai301-fa26-s3 in a Postgres + Docker environment. The repro comment at https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-5906185951 shows the failing request:

```
HTTP/1.1 503 Service Unavailable
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy",...}}}
```

and the server log:

```
postgres_health_check_failed error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
```

The root cause is the missing `text()` wrapper on the DB probe — not the Postgres connection, not the health-check response structure.

## Scope

**What I'll change:**
- `api/routes/health.py` — wrap the `"SELECT 1"` argument in `sqlalchemy.text(...)`.
- `tests/unit/test_health.py` (or the equivalent test file for health routes) — add a regression test.

**What I won't:**
- I won't touch the Redis health check (`settings.redis_host`) — that's a separate, already-filed bug (#62).
- I won't refactor the health check's overall structure or error handling.
- I won't change the response schema.

## Files to touch

- `api/routes/health.py` — the health_check handler, the `db.execute("SELECT 1")` line.
- `tests/unit/test_health.py` — a new test asserting the endpoint reports `dependencies.postgres == "healthy"`.
- `pyproject.toml` — narrow the `disable_error_code` for `api.routes.health` by dropping `call-overload`, since the text() wrapper resolves it. Keep `attr-defined` (that's #62's concern).

## Approach

1. Add `from sqlalchemy import text` at the top of `api/routes/health.py`.
2. Replace `await db.execute("SELECT 1")` with `await db.execute(text("SELECT 1"))`.
3. Add a regression test in `tests/unit/test_health.py` that hits `/health` via the FastAPI test client and asserts `dependencies.postgres == "healthy"`. The overall status may still be 503 due to the Redis check (#62); the test isolates this fix.
4. Update `pyproject.toml`'s mypy override for `api.routes.health`: remove `call-overload` from the `disable_error_code` list, since the fix resolves the type error.

## Test plan

Before the fix (already captured in the unit 2 repro):
```
$ curl.exe -i http://127.0.0.1:8000/health
HTTP/1.1 503 Service Unavailable
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy",...}}}
```

After the fix, I expect:
```
$ curl.exe -i http://127.0.0.1:8000/health
HTTP/1.1 503 Service Unavailable
{"detail":{"status":"unhealthy","dependencies":{"postgres":"healthy","redis":"unhealthy","vector_db":"healthy"},...}}
```

(Overall status stays 503 until #62 is fixed. My fix only flips `postgres` from `"unhealthy"` to `"healthy"`, which is what the regression test asserts on.)


The regression test will run these assertions under pytest so the fix can't silently regress.

## Risks and unknowns

- The Redis health check fails independently (`settings.redis_host` AttributeError). If the postgres fix lands but Redis still fails, the endpoint may still return 503. I'll assert on `dependencies.postgres == "healthy"` rather than the overall status, so the test isolates this fix.
- `settings.vector_db_url` is checked as a string, not a live connection, so the vector_db field will report healthy regardless. Not my concern here.
- I don't know whether the repo's CI runs the health endpoint against a real Postgres or mocks it; if it mocks, the test may need to patch the session. I'll check `tests/` before writing the test.

## Deviations

Nothing deviated from the plan. The fix was a one-line change in `api/routes/health.py`, the pyproject.toml override narrowed as planned, and `tests/unit/test_health.py` was created with a regression test asserting `dependencies.postgres == "healthy"`. The expected before/after behavior matched: `postgres` flipped from unhealthy to healthy while the overall status remained 503 due to #62.