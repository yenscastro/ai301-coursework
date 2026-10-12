# Unit 2 reproduction

## Claim comment

Link: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-5904199473

Text:
Hi, I'd like to take this one as a first contribution.

I'm seeing that the DB probe in api/routes/health.py passes the raw string 'SELECT 1' to session.execute(), which fails under SQLAlchemy 2.x unless wrapped in ext(). I'll reproduce it against the current main, record the environment and exact output, and post a full reproduction report here.

If it turns out to be the SQLAlchemy version mismatch described in the issue, I'll note that; if the behavior turns out to be different, I'll report what I actually find.

## Repro comment

Link: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-5906185951

Text:
Result: reproduced.

Environment:

    OS: Windows 11
    Python: 3.12.10 (venv)
    SQLAlchemy: 2.1.1
    FastAPI: 0.142.1
    asyncpg: 0.31.0
    Postgres: postgres:16-alpine (Docker container pathreview-ai301-fa26-s3-db-1, host port 5433)
    Redis: redis:7-alpine (Docker container pathreview-ai301-fa26-s3-redis-1, host port 6379)
    Repo commit: 2f4e82f

Setup:

    python -m venv .venv; .\.venv\Scripts\Activate.ps1
    pip install -e .
    Copied .env.example to .env (Postgres URL points at the Docker container on localhost:5433).
    docker compose up -d
    alembic upgrade head

Reproduction steps:

    uvicorn api.main:app --reload --port 8000 — server starts cleanly ("Application startup complete.").
    In another shell: curl.exe -i http://127.0.0.1:8000/health

Output (HTTP response):

HTTP/1.1 503 Service Unavailable
date: Wed, 30 Sep 2026 06:47:33 GMT
server: uvicorn
content-length: 184
content-type: application/json
vary: Origin
x-request-id: 4e6a756e-ded7-48f9-aaf7-5687a8483a46

{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-30T06:47:34.588420"}}

Server-side log (uvicorn console) on the request:

2026-09-29 23:47:34 [error] postgres_health_check_failed error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=4e6a756e-ded7-48f9-aaf7-5687a8483a46
INFO:     127.0.0.1:60614 - "GET /health HTTP/1.1" 503 Service Unavailable

Corresponding code — api/routes/health.py, in health_check:

await db.execute("SELECT 1")   # raw string; raises sqlalchemy.exc.ArgumentError under SQLAlchemy 2.x

Expected: /health returns 200 with all dependencies healthy (the DB, Redis, and vector DB are all up in this environment).

Actual: /health returns 503 with postgres: unhealthy in the body, and the server log shows the SQLAlchemy ArgumentError. The behavior matches #61 exactly: the raw string passed to db.execute() is not accepted under SQLAlchemy 2.x and must be wrapped in text("SELECT 1").

Notes on the setup path:

    The repo's init_db() calls Base.metadata.create_all, which hits an unrelated pre-existing schema bug (a duplicate ix_profiles_user_id index defined in the model layer). I reset the Postgres volume (docker compose down -v) and ran the repo's Alembic migrations (alembic upgrade head) instead. That blocker is separate from the health check bug and does not affect this reproduction.
    The /health response also shows redis: unhealthy from a separate bug ('Settings' object has no attribute 'redis_host'), filed independently as issue Health check references settings.redis_host, which does not exist on Settings #62. It is not the behavior Health check DB probe passes a raw SQL string, which fails under SQLAlchemy 2.x #61 describes and is not reported here.



## Reflection

**What was hardest about reproducing this bug?**
<Write 2-3 sentences in your own words. Example: The hardest part was getting the app to boot at all — the project's init_db() hits a duplicate-index bug on first run, so I had to reset the Postgres volume and run the Alembic migrations manually instead of relying on create_all. Once the app was running, the actual bug reproduced on the first health-check request.>

**What did you learn that you did not expect?**
<Write 2-3 sentences. Example: I did not expect the health endpoint to catch the SQLAlchemy exception and degrade to a 503, rather than crashing the request. That makes the bug quieter than a stack trace — the endpoint returns a structured response, so the only way to notice is by reading the body or the server log. I also did not expect a second health bug (settings.redis_host) to show up in the same response.>

**What would you do next if you kept working on this issue?**
<Write 2-3 sentences. Example: The fix is one line: wrap the raw string in sqlalchemy.text(). I would add a regression test that hits /health and asserts the postgres dependency reports healthy, and I would note the Redis issue as a separate follow-up rather than bundling it into this PR.>

## Run history

1. Baseline full run: 17/20. Disagreements on pkg-09 and pkg-10 (both gold accept, my rubric rejected on behavior_matches_issue), and pkg-16 (gold reject, my rubric accepted).

2. First revision: fixed promise_not_assertion's scope for pkg-01 (claim-only drafts need future tense; full packages can assert reproduction backed by their report). Re-ran --only pkg-01: accept.

3. Second revision: split behavior_matches_issue so it only applies when outcome_honest says "reproduced" — honest cannot-reproduces (pkg-09, pkg-10) pass by default. Re-ran --only pkg-09,pkg-10,pkg-13,pkg-14: 5/5.

4. Third revision: tightened environment_recorded to require an explicit callout when the report runs on a version behind the issue's stated target. Re-ran --only pkg-16 with canaries pkg-07,pkg-18. This fixed pkg-16 (gold reject) but regressed pkg-07 (gold accept), because the check wasn't direction-aware: it rejected newer-version runs too.

5. Fourth revision: rewrote environment_recorded to fail only when the report runs BEHIND the target with no callout; newer versions pass. Added a new required check repro_self_contained to reject reports whose steps depend on private artifacts (pkg-18's monorepo, unshareable config). Re-ran --only pkg-07,pkg-18,pkg-16,pkg-09,pkg-10,pkg-19: 6/6.

6. Confirming full run with --save-run eval-run.txt: 20/20, all categories full (clear-accept 8/8, disclosure 1/1, no-evidence 4/4, unfollowable-comms 3/3, wrong-target 4/4).

## Package analysis

**pkg-16** (pandas-dev/pandas#66656). My rubric initially said accept; the gold label said reject. The candidate report ran on pandas 1.5.3 while the issue was filed against pandas 3.0.5 / main, and the report never called out the version difference. The repo's bug-report template explicitly asks reporters to confirm the bug exists on the latest version and on the main branch. My environment_recorded check at that point only verified presence of OS/version/revision signals — pkg-16 had all three (Ubuntu, Python 3.10, pandas 1.5.3) — so it passed a report whose environment didn't actually match the target the repo's conventions care about.

I tightened the check to require the report to either match the issue's stated target version or explicitly call out the difference and explain why it doesn't invalidate the reproduction. That flipped pkg-16 to reject. But the first version of the tightening wasn't direction-aware: it also rejected pkg-07, which runs on p5.js 1.11.7 while the issue was filed against 1.9.4/1.10.0 — a newer version, still reproducing. The final wording fails only when the report runs BEHIND the target with no explanation; running on a later version passes. That distinction is what got pkg-07 back into agreement without losing pkg-16.

## Check rationale

My final environment_recorded check reads, verbatim:

> "The report's environment section, read against the issue's stated target and the repo's bug-report template. | The report names (a) the OS, (b) the runtime/language versions, and (c) the repo revision. When the issue or the repo's template states a target version and the report runs on a version BEHIND that target with no explanation, fail. Running on the same version, a newer version, or a version ahead of the target passes. Running on a different OS or shell than the issue reports is acceptable if the report shows the issue's behavior reproduced (or, for a cannot-reproduce, explains why the difference matters)."

The word "BEHIND" in caps is the key. My earlier wording treated any version mismatch as a fail, which rejected pkg-07 (running ahead of the target) as well as pkg-16 (running behind, no callout). The direction matters: running on a later version with the bug still present is stronger evidence, not weaker. Running on an earlier version risks reporting on a behavior that's already fixed upstream. The final wording captures that asymmetry, and the "explicit callout" clause is what the repo's template actually asks for when it says "confirm on the latest version and main."

## Trade-offs

The tightened environment_recorded is riskier than the original. It now fails a report that runs on an older version without explanation, even if the bug is real and reproduces — a valid reproduction on an old environment would be rejected. I accepted that risk because the eval showed the failure mode (pkg-16: a report that runs on a version behind the issue's target, with no callout) was more common and more consequential than a false positive on a legitimate old-version run. If the fix lands in a repo whose conventions don't ask for version confirmation, the direction-aware wording means newer-than-target runs still pass, which covers most of the false-positive surface.

The second trade-off is adding repro_self_contained as a new required check. It rejects reports whose steps depend on private artifacts (a company monorepo, an internal config). pkg-18's report pasted a stack trace and named the failing file, but every reproduction step was "checked out our private repo, used our internal config" — a stranger cannot re-run it. I traded flexibility (reports that summarize private setup) for the guarantee that an accepted package is actually reproducible by a reader. The eval's unfollowable-comms category (3/3 in the final run) suggests that trade is aligned with what the gold labels care about.