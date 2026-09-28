# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

---

## Your identity upstream

**GitHub username**

rahp124

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-5866593136

Hi, I'm working through this repo as a course assignment (AI301) and would like to claim issue #62 for my Unit 2 reproduction. From reading the code, the `/health` route's Redis probe in `api/routes/health.py` appears to build its client from `settings.redis_host` and `settings.redis_port`, while `Settings` in `core/config.py` only defines a single `redis_url` — I'm treating that as a hypothesis to investigate, not a confirmed cause yet.

My plan is to set up the project and its backing services locally, hit `GET /health` with Redis running, and see whether that mismatch is actually what produces the reported 503/`AttributeError`. I'll report back with what I find, including if my setup doesn't reproduce the reported behavior.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-5866677310

Reproduced this on commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088` (current `main`).

Environment: macOS 15.6, Apple Silicon (arm64), Python 3.12.0, Docker 29.2.1 / Compose v5.0.2, fastapi/uvicorn as pinned in `pyproject.toml`. Backing services from `docker compose up -d` (Redis reachable, confirmed with `redis-cli ping` -> `PONG`).

Steps:

1. `cp .env.example .env` (defaults unchanged).
2. `docker compose up -d`, waited for `db` and `redis` to report healthy.
3. `python3.12 -m venv .venv && .venv/bin/pip install -e ".[dev]"`, `.venv/bin/alembic upgrade head`, `.venv/bin/python scripts/seed_db.py`.
4. Started the API only: `.venv/bin/uvicorn api.main:app --host 0.0.0.0 --port 8000`.
5. `curl -s -w "\nHTTP_STATUS:%{http_code}\n" http://localhost:8000/health`

Expected: with Redis reachable (confirmed via `redis-cli ping`), the health check's Redis probe should report `"redis": "healthy"`.

Actual:

```
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-28T08:53:39.951723"}}
HTTP_STATUS:503
```

The server log for that same request:

```
2026-09-28 01:53:40 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'" request_id=a1bb4c8d-cab3-4b23-bb12-d05ac7a5adcc
```

This matches the issue exactly: `api/routes/health.py` builds its Redis client from `settings.redis_host` (line 47) and `settings.redis_port` (line 48), but `Settings` in `core/config.py` only defines `redis_url` (line 14) — there is no `redis_host`/`redis_port` field. The resulting `AttributeError` is caught by the probe's `except Exception`, so the health check reports Redis as down even with Redis actually running and reachable, which is exactly what I confirmed with `redis-cli ping`.

Note: the same response also shows `"postgres": "unhealthy"`, with a separate, unrelated log line (`error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"`). That looks like a different, pre-existing issue in the Postgres probe, not something this issue describes or that I investigated further — flagging it only so it isn't confused with the Redis behavior above.

## Eval iterations

**Run history**

1. Smoke run, `--limit 3`: 3/3 agreement (pkg-01, pkg-02, pkg-03).
2. First full run (20 packages, initial rubric): 20/20 agreement, all category floors met.
3. Confirming full run with `--save-run eval-run.txt`: 19/20 agreement — disagreed on `pkg-03` (gold `accept`, my rubric said `reject`, failing `disclosure-when-required`).
4. Revised `disclosure-when-required` (see Check rationale below), re-synced the installed skill, and re-ran the disagreement plus canaries with `--only pkg-03,pkg-20,pkg-05,pkg-07,pkg-19`: 5/5 agreement, no regressions.
5. Final confirming full run with `--save-run eval-run.txt` (the run committed in `eval-run.txt`): **20/20 agreement, bar 18/20: PASS, all category floors met** (`clear-accept 8/8, disclosure 1/1, no-evidence 4/4, unfollowable-comms 3/3, wrong-target 4/4`).

**Package analysis**

`pkg-09` (source `sharkdp/fd#2033`, gold `accept`, category `clear-accept`). My rubric also scored it `accept`. This package is an honest cannot-reproduce: the candidate spent real effort trying to trigger the issue's second scenario (an `--exec-batch` command overtaking another when it hits the argument-size limit first), showed the actual marker-order log from five runs, and stated plainly that they never saw a `TWO` marker overtake a `ONE` marker — then named what might have differed (uniform file-name lengths, a 2 MiB `ARG_MAX` they had no CLI knob to lower). My `trigger-fidelity` check reads this as a pass because the check's condition explicitly treats "an honest, evidenced cannot-reproduce of the same trigger" as satisfying the behavior family, not just a successful repro; my `honest-claim` check also passes it because the stated conclusion ("I could NOT reproduce... this report is about scenario 2 only") never claims more than the shown log demonstrates. A rubric that only recognized "artifact shows the bug" as a pass would have wrongly rejected this package.

**Check rationale**

From `rubric.md`:

> `trigger-fidelity` — "The steps taken target the same trigger conditions the issue describes, not an altered or adjacent one (a different input, a different code path, a different error/exit code). This holds whether the outcome is 'reproduced' or an honest, evidenced 'could not reproduce' of that same trigger — an honest cannot-reproduce that names what was tried and what differed is a pass; a result from a changed trigger, or an artifact that only shows something adjacent to the bug (e.g. the tool running fine), is a fail."

I wrote it this way after reading `pkg-02` and `pkg-08` (both `wrong-target`, both rejected in gold) side by side with `pkg-09` and `pkg-10` (both `clear-accept`, both honest cannot-reproduces). My first instinct was a single "artifact matches the issue's behavior" condition, which would have implicitly failed every honest cannot-reproduce package for not showing the bug. Reading the gold notes for `pkg-09`/`pkg-10` made clear that the eval set specifically rewards a real, evidenced attempt at the *same trigger* even when the writer never manages to break it — so I split the check into "same trigger" (which an honest failed attempt still satisfies) from "shows the bug" (which it doesn't have to), and pushed the overclaiming risk into the separate `honest-claim` check instead.

**Trade-offs**

The `disclosure-when-required` revision (narrowing the check to fire only on an explicit "state that AI was used and to what extent" ask, not a general "written by a human / in your own words" rule) is the one place I loosened a check mid-run, and it flipped exactly one package: `pkg-03` (`BurntSushi/ripgrep#2779`), whose repo policy says comments "must be written by humans in their own words" but never asks for an AI-use disclosure statement — my original wording had conflated the two and wrongly failed it. Before trusting the loosened check, I re-ran it against three canaries alongside the fix: `pkg-20` (the one-item `disclosure` category, where ghostty's policy *does* explicitly ask for disclosure — still correctly rejected on this check, so the loosening didn't erase the category the floor exists for), and `pkg-05`/`pkg-07` (two other `clear-accept` packages with permissive or disclosure-satisfied policies, both still agreed). All three held, so the loosening is scoped to the human-voice-vs-disclosure distinction it was meant to fix and didn't touch anything else in the run.
