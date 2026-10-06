# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

Lementknight

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-6007296787

> I'd like to claim this one as my first contribution to Path Review.
>
> I've been working through the health check code to understand how the app reports on its dependencies, and the `db.execute("SELECT 1")` probe in `api/routes/health.py` caught my eye: the issue says SQLAlchemy 2.x wants textual SQL wrapped in `text()`, so a reachable database could be reported as down.
>
> I haven't reproduced it yet. Next I'll bring up the documented Docker Compose stack on macOS from a fresh clone of my fork at `2f4e82f`, call `GET /health`, and check whether the 503 and the `ArgumentError` from the issue show up on my machine, with the server log for the same request. I've seen the other claims and repros on this thread, but I'll run my own steps in my own environment rather than rely on them. I'll post the exact commands, versions, and output as a follow-up comment on this issue.
>
> In short: I'm claiming #61, and my next step is an independent repro of the `/health` failure, which I'll report here.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-6007297812

> Reproduced on my own machine. I wanted to see the `/health` failure from #61 through the real documented stack rather than rely on the earlier reports, so here are my exact steps and output.
>
> **Environment**
>
> - PathReview at `2f4e82f` (fork `Lementknight/pathreview-ai301-fa26-s3`), `git status --porcelain --untracked-files=no` empty.
> - macOS 27.0, OrbStack (Docker 29.4.0, Compose 5.1.2), Python 3.14.7 in a fresh `.venv`.
> - `pip install -e ".[dev]"` resolved SQLAlchemy 2.1.3, asyncpg 0.31.0, FastAPI 0.142.2, structlog 26.1.0.
> - Services from `docker-compose.yml`: postgres:16-alpine (host port 5433), redis:7-alpine, chromadb 0.4.22.
>
> **Deviation from `docs/SETUP.md`, stated up front:** I ran the setup steps by hand and skipped the frontend `npm install` and `make run`, because `/health` only needs the API. I started the API with `uvicorn api.main:app --port 8000` instead.
>
> **Steps**
>
> 1. `cp .env.example .env` (defaults, no edits)
> 2. `docker compose up -d`, then wait until `docker compose ps` shows `db` and `redis` healthy
> 3. `python3 -m venv .venv && .venv/bin/pip install -e ".[dev]"`
> 4. `.venv/bin/alembic upgrade head` and `.venv/bin/python scripts/seed_db.py`
> 5. `.venv/bin/uvicorn api.main:app --port 8000`
> 6. In another shell: `curl -i http://localhost:8000/health`
>
> **What I saw**
>
> ```
> HTTP/1.1 503 Service Unavailable
> {"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"}, ...}}
> ```
>
> Server log for the same request:
>
> ```
> [error] postgres_health_check_failed error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
> [error] redis_health_check_failed    error="'Settings' object has no attribute 'redis_host'"
> [debug] vector_db_health_check_passed
> ```
>
> So the `ArgumentError` the issue describes shows up exactly as reported, from `api/routes/health.py`.
>
> **Control: the database is reachable the whole time**
>
> - `docker compose exec db psql -U pathreview -d pathreview_dev -c "SELECT 1"` returns `1`.
> - The app's own session (`core.database.AsyncSessionLocal`) running `text("SELECT 1")` returns `1`.
>
> So the failure is the raw string, not the connection.
>
> **One thing this issue does not cover:** the Redis line in that same response is a different error (`'Settings' object has no attribute 'redis_host'`). That looks like #62, so I'm leaving it out of this report. It does mean `/health` will still answer 503 after the Postgres probe is fixed until that one is too.
>
> **Not tried:** Linux and Windows, and SQLAlchemy 2.0.x (I got 2.1.3 from the unpinned install).
>
> In short: with the documented stack up, `GET /health` returns 503 with `"postgres": "unhealthy"` and the `ArgumentError` in the log, while Postgres answers `SELECT 1` fine, matching #61.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke run (`--limit 3`, pkg-01/pkg-02/pkg-03): 3/3 agreement.
2. Full run: 17/20 agreement — below the 18/20 bar. All three misses
   (pkg-05, pkg-09, pkg-10) were gold `accept` packages my rubric
   rejected.
3. Canary run (`--only pkg-05,pkg-09,pkg-10,pkg-20,pkg-06,pkg-18,pkg-08,pkg-11`)
   after revising two checks: 8/8 agreement, including the three
   previous misses and five canaries from the categories the revision
   could have touched.
4. Confirming full run: 20/20 agreement — bar: PASS. This is the run
   committed as `eval-run.txt`.

**Package analysis**

`pkg-09` (sharkdp/fd#2033): gold verdict `accept`, my rubric's final
verdict `accept` — agree. The claim/repro pair is an honest
cannot-reproduce: the reporter ran the exact scenario-2 trigger
(argument-size-limit batch reordering), showed the log from five real
attempts, and stated plainly that no reordering occurred, naming what
likely differed (uniform file-name lengths vs. the varied lengths the
bug may need). My rubric reads this as `accept` because
`behavior-matches-issue` explicitly passes a genuine, evidenced
cannot-reproduce, and `honest-outcome` passes it too since the stated
conclusion never claims more than the artifact shows. Before a
revision (see Check rationale), my rubric rejected this same package:
`behavior-matches-issue` had no cannot-reproduce exception, so an
honest "it didn't happen" always failed a check that demanded the
artifact match the issue's behavior — which a cannot-reproduce report
can never do by definition.

**Check rationale**

`behavior-matches-issue`, as it now reads in `tools/repro-check/rubric.md`:

> Pass if the artifact shows the same behavior the issue reports — same
> error, symptom, or exit condition — not an adjacent one produced by a
> different input or a different failure path. An honest, evidenced
> cannot-reproduce also passes this check: a genuine attempt at the
> issue's exact trigger, with the artifact from that attempt shown,
> that plainly states the reported behavior did not occur. Fail if the
> artifact shows a different symptom, a gracefully-handled case
> narrated as the issue's crash (or vice versa), a cannot-reproduce
> with no real attempt shown, or no artifact at all.

It reads this way because the first full run showed this check
structurally conflicting with `honest-outcome` on `pkg-09` and
`pkg-10`: both are real, evidenced cannot-reproduce reports, and
`honest-outcome` correctly passed them, but the original
`behavior-matches-issue` had no exception for a cannot-reproduce
outcome, so it failed them anyway — and since verdict requires every
`required` check to pass, an honest cannot-reproduce could never
accept under the original wording, which contradicts the proof family
the lecture named ("an evidenced cannot-reproduce is a pass, a
confident wrong-target is not"). I added the explicit carve-out rather
than weakening the check generally, so a report that merely lacks any
artifact, or narrates a mismatched artifact as a match, still fails.

**Trade-offs**

The same revision also loosened `steps-rerunnable`, from requiring
every step to be a literal executable command to requiring only that
the commands which trigger and surface the behavior are exact, with
setup steps allowed to name precisely what's needed rather than
pasting a literal file dump (this is what let `pkg-05`'s prose-described
`env.yml` setup pass alongside its two exact commands). The trade-off:
a looser steps check risks accepting a report whose real setup is
under-specified as long as the two headline commands look exact. I
checked this against the packages built to catch exactly that failure
mode — `pkg-06` (no environment recorded at all, steps omit the
Windows driver) and `pkg-18` (repro lives in an unshared private
monorepo) — by re-running them with `--only` as canaries alongside the
fix. Both still correctly reject after the change, so the looser
wording didn't buy back the packages it was never meant to pass.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
