# Plan: #61 health check DB probe passes a raw SQL string

## Diagnosis

The Postgres probe in `api/routes/health.py` calls `await db.execute("SELECT 1")` with a plain string. SQLAlchemy 2.x refuses raw strings and raises `ArgumentError` while coercing the statement, before any connection is used. The route's `except Exception` swallows it and reports Postgres as unhealthy.

This follows from my own repro (posted on #61, at `2f4e82f` with the documented Compose stack):

- `curl -i http://localhost:8000/health` returned `503` with `"postgres":"unhealthy"`.
- The server log for the same request: `postgres_health_check_failed error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"`.
- Control 1: `docker compose exec db psql -U pathreview -d pathreview_dev -c "SELECT 1"` returned `1`, so Postgres was reachable the whole time.
- Control 2: the app's own session (`core.database.AsyncSessionLocal`) running `text("SELECT 1")` returned `1`.

The error text names the raw string, and the same session succeeds once the string is wrapped, so the string is the cause and the connection is not. It is the only raw-string `execute()` in the repo: every other call in `api/` and `core/` passes a `select(...)` statement.

## Scope

In scope: `api/routes/health.py`, wrapping the probe statement in `sqlalchemy.text()`, and one new unit test file `tests/unit/test_health.py`.

Not in scope, and I won't touch:
- The Redis probe. It fails separately with `'Settings' object has no attribute 'redis_host'`, which is #62. After my change `/health` still returns 503 until #62 is fixed.
- The broad `except Exception` around the probe. Whether it should catch a code defect is a design question for the maintainers, not part of this fix.
- The `disable_error_code` suppressions for `api.routes.health` in `pyproject.toml`. CONTRIBUTING says to remove a seeded bug's suppression when you fix it, so I checked each one against the unfixed and fixed file. `attr-defined` is the Redis probe (#62), `call-overload` is the `redis.Redis(...)` call at the Redis probe, and `index` is the dict assignments. Dropping any of them reports the same errors before and after my change, so none belongs to #61. There is also no `xfail` marker for #61 in `tests/`.
- Any other part of the health route, other routes, config, or dependencies.

## Files I will touch

- `api/routes/health.py`: add `from sqlalchemy import text` and change line 32 to `await db.execute(text("SELECT 1"))`.
- `tests/unit/test_health.py` (new): the regression test below.

## Approach

Branch on my fork: `fix/61-health-check-text-wrap`.

1. Make the one-line change and add the import, in that file only.
2. Add a unit test that calls `health_check` with a SQLAlchemy `Session` bound to in-memory SQLite, wrapped so `execute` is awaitable. SQLAlchemy's own statement coercion then runs, with no Postgres needed. It asserts `dependencies.postgres == "healthy"`. Before the fix the raw string raises `ArgumentError` and the probe reports `unhealthy`, so the test fails; after the fix it passes.
3. Re-run my repro against the real stack and compare before and after.
4. Run `make check && make test-unit`, as `docs/CONTRIBUTING.md` asks.

## Test plan

My unit 2 repro steps, re-run after the fix:

1. `docker compose up -d`, then `uvicorn api.main:app --port 8000`.
2. `curl -s http://localhost:8000/health`.

Expected after the fix: the response has `"postgres":"healthy"` and the log has no `postgres_health_check_failed` line. The overall status still returns 503 because of the Redis error from #62, so I check the `postgres` field, not the status code. Before the fix, the same request gives `"postgres":"unhealthy"` plus the `ArgumentError`.

The new unit test fails on `2f4e82f` (probe reports `unhealthy`) and passes with the change.

## Risks and unknowns

- I tested on SQLAlchemy 2.1.3 only (unpinned install). `text()` is the documented form in 2.0.x as well, but I haven't run 2.0.x.
- I haven't tested on Linux or Windows.
- The unit test runs against SQLite, not Postgres, so it proves SQLAlchemy accepts the statement but not a live Postgres query. The manual Postgres re-run covers that part.
- Prior art: three open PRs touch this area. #82 makes the same `text()` change and also fixes #62, with no test. #96 makes the same `text()` change and adds `tests/unit/test_health_route.py`, using the same SQLite approach as my test. #93 fixes the Redis probe (#62). I read the diffs of #82 and #96. My change overlaps #96 almost entirely. I'll check it against their diffs again before opening a PR. If one of them merges first, I'll rebase and keep only what still adds coverage, or drop mine.
- Whether a maintainer wants the Redis fix in the same PR is a question for the thread. I'm assuming no.

## Deviations

Nothing changed from the plan I posted. The build touched `api/routes/health.py` and added `tests/unit/test_health.py`, on the branch `fix/61-health-check-text-wrap`, and nothing else. The test uses an in-memory SQLite session, as the posted plan says. Both checks I promised came out as expected: the new test fails on `2f4e82f` and passes with the fix, and my repro against the Compose stack now reports `"postgres":"healthy"` with no `ArgumentError`, while the overall status stays 503 because of the Redis bug (#62). #82, #93 and #96 were all still open when I finished, so the overlap I described in my comment is unchanged.
