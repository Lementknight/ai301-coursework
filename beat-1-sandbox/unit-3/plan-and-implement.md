# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

Lementknight

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-6007407656

> Here is my plan for #61, built from my own repro above (commit `2f4e82f`, Compose stack, `GET /health`).
>
> I wanted to be sure the cause is the raw string and nothing else, so I used the controls from my repro: `psql` and the app's own session both run `SELECT 1` fine, and the same session accepts `text("SELECT 1")`. The `ArgumentError` is raised before any connection is used, so the cause is the unwrapped string at `api/routes/health.py:32`.
>
> **Change:** wrap the probe in `text()` in that file. It is the only raw-string `execute()` call in the repo.
>
> **Not touching:** the Redis probe (that is #62, and `/health` will still return 503 until it is fixed), and the broad `except Exception` around the probe, which I think is a separate question for you. I also checked the `api.routes.health` mypy suppressions in `pyproject.toml` against CONTRIBUTING's "remove the suppression" rule: `attr-defined`, `call-overload` and `index` give the same errors before and after this fix, so none of them is tied to #61 and I'm leaving them.
>
> **Test:** a new unit test that runs the real `health_check` against an in-memory SQLite session and asserts `dependencies.postgres` is `healthy` (it reports `unhealthy` on the current code), plus re-running my repro: `postgres` should read `healthy` and the `ArgumentError` should be gone from the log.
>
> **Open PRs on this line:** I read the diffs of #82 and #96. #82 makes the same `text()` change and also changes the Redis probe (#62), with no test. #96 makes the same `text()` change and adds `tests/unit/test_health_route.py`, which is close to what I'm planning, so my change overlaps it almost entirely. What's mine is that it's built on my own repro and test. I'll compare my diff against both before opening anything, and if one of them merges first I'll rebase and keep only what still adds coverage, or drop mine.
>
> **Not tried:** SQLAlchemy 2.0.x, Linux, Windows.
>
> I'm using Claude Code for the edits and will read every diff myself before I open the PR.
>
> In short: one-line `text()` fix in `health.py` with a regression test, leaving Redis (#62) alone, and overlapping #96 closely enough that I'll defer to it if it lands first.

---

## Your branch

**Branch**

`fix/61-health-check-text-wrap`

**Evidence**

Same repro as Unit 2 (issue #61, commit `2f4e82f`, Compose stack up: `docker compose up -d`, `.venv/bin/alembic upgrade head`, `.venv/bin/uvicorn api.main:app --port 8000`). Command for both runs: `curl -i http://localhost:8000/health`.

**Before the fix** (unit 2 run, `2f4e82f`):

```
$ curl -i http://localhost:8000/health
HTTP/1.1 503 Service Unavailable
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-10-06T01:10:46.017634"}}

server log:
[error] postgres_health_check_failed error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
[error] redis_health_check_failed    error="'Settings' object has no attribute 'redis_host'"
[debug] vector_db_health_check_passed
```

**After the fix** (branch `fix/61-health-check-text-wrap`):

```
$ curl -s -i http://localhost:8000/health
HTTP/1.1 503 Service Unavailable
{"detail":{"status":"unhealthy","dependencies":{"postgres":"healthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-10-06T01:25:11.113409"}}

server log:
[debug] postgres_health_check_passed
[error] redis_health_check_failed    error="'Settings' object has no attribute 'redis_host'"
[debug] vector_db_health_check_passed
```

`postgres` flips from `unhealthy` to `healthy` and the `ArgumentError` is gone. The overall status stays 503 because of the Redis error, which is #62 and out of scope, as the plan says.

**Regression test** (`tests/unit/test_health.py`), run on the original code and then the fixed code:

```
$ git stash push api/routes/health.py && .venv/bin/pytest tests/unit/test_health.py -q
FAILED tests/unit/test_health.py::test_postgres_probe_reports_healthy_when_database_answers
1 failed, 2 warnings in 0.48s
$ git stash pop && .venv/bin/pytest tests/unit/test_health.py -q
1 passed, 2 warnings in 0.97s
```

**Repo checks on the branch:** `ruff check` and `black --check` clean on both files, `mypy api/ core/` "Success: no issues found in 27 source files", `pytest tests/unit -m unit` "376 passed, 53 xfailed".

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke run (`--limit 3`, pkg-01, pkg-02, pkg-03): 3/3 agreement.
2. Full run (20 packages), saved with `--save-run eval-run.txt`: 20/20 agreement, bar PASS, every category matched (`clear-accept 7/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`).
3. Re-grade of `pkg-16` only (`--only pkg-16`), to read the per-check reasoning for the package analysis below: 1/1 agreement. This is a partial run and wrote nothing to `eval-run.txt`.

The last full run is the one in `eval-run.txt`: 20/20 agreement.

**Package analysis**

`pkg-16` (pandas-dev/pandas#57666, category `wrong-cause`): the gold verdict is `reject`, and my rubric also rejected it, so they agree. The plan is long and confident and blames the post-read cast, but the package's repro step 4 shows the leading zeros are already gone in pyarrow's inferred int64 table before any cast runs.

My rubric failed four checks. The decisive one is `cause-fits-repro`: "Repro step 4: zeros already gone in the parsed int64 table 'before any cast to string could run', contradicting the plan's cast-side diagnosis." `bounded-scope` also failed, because the plan's own scope says "Not in scope: pyarrow's reader options", which is exactly the component step 4 shows is broken. `thread-and-policy-fit` failed because the comment never engages the fix direction a commenter (dxdc) gave in the thread, and `honest-unknowns` (preferred, so it never changes the verdict) failed because the plan states no unknowns and claims the cast-side cause as verified.

My rubric reads it this way because the procedure reads the repro evidence before the plan, and `cause-fits-repro` tests the stated cause against each step and control rather than asking whether a cause is stated. The same trap is the one in the in-class calib-03 package, where the sample rubric accepted a confident cause that the repro contradicted.

**Check rationale**

From `tools/plan-check/rubric.md`, exactly as it reads now:

| cause-fits-repro | The plan's stated cause (Diagnosis / Cause), read against the repro evidence block: its numbered steps, every control run (a step that changes one thing and keeps the rest), and its Actual line. A cause cited from a thread comment is read the same way. | Every observation and control in the repro evidence is what the stated cause predicts. Fail if any step or control contradicts it (the bug still happens with the suspected part bypassed or removed, or the suspected part already behaves correctly in a control), or if the repro evidence points at a different component than the plan changes. Unclear only if the repro evidence has no step or control that could confirm or rule out the cause. | required |

It reads this way because of calib-03 in the in-class activity. The sample rubric's "passes if the plan says what causes the bug" passed a plan that copied a confident root cause from a thread comment, and the repro's control runs showed that cause could not be right. So this check does not ask whether a cause is stated. It tests the stated cause against every observation and control in the repro evidence. "Fail if any step or control contradicts it" makes a contradicting control decisive, and the clause about a cause that only cites the thread says the repro decides, not the thread. I wrote `unclear` for the case where the repro has no step or control that could confirm or rule out the cause, and the verdict rule treats `unclear` on a required check as a fail, so a plan I can't verify is not ready.

**Trade-offs**

The `decisive-test` check accepts manual or automated tests, as long as they name an observable outcome that differs before and after the fix and tie back to the repro. I chose that over requiring an automated test, which the in-class sample rubric did. Requiring one would have wrongly failed calib-01, a correct accept whose test is the repro steps re-run, and that is a plan I want to pass. The cost is that a plan with a manual-only test gets in, which is a case I accept: a manual re-run of the repro is still decisive when it names the expected result.

The full run came back 20/20 on the first try, so I did not loosen or tighten any check during the run, and no canary re-runs with `--only` were needed. I only re-graded `pkg-16` alone, and it still agrees.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
