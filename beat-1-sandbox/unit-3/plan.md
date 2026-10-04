# Plan: issue #62 — health check references `settings.redis_host`, which does not exist on `Settings`

## Diagnosis

`api/routes/health.py`'s Redis probe (lines 46-51) builds its client with
`redis.Redis(host=settings.redis_host, port=settings.redis_port, db=0,
decode_responses=True)`. `Settings` in `core/config.py` defines no `redis_host` or
`redis_port` field — only a single `redis_url: str = Field(default="redis://localhost:6379/0")`
(line 14). Accessing `settings.redis_host` raises `AttributeError: 'Settings' object has
no attribute 'redis_host'`, which the probe's bare `except Exception` (line 55) catches
silently, so the probe always reports `redis: unhealthy` — even when Redis is reachable.

This is exactly what my Unit 2 reproduction evidence shows: with `docker compose up -d`
running (Redis confirmed reachable via `redis-cli ping` -> `PONG`), `GET /health` still
returns `503` with `"redis":"unhealthy"`, and the server log for that same request reads
`redis_health_check_failed error="'Settings' object has no attribute 'redis_host'"`. The
log line names the exact attribute error the code predicts, and the artifact's "redis
unhealthy despite a successful PONG" result is only explainable by the probe's exception
path firing before it ever calls `.ping()` — there is no other path in this route that
produces that combination. `pyproject.toml`'s mypy override block independently confirms
this is the seeded bug behind #62 (and the related #61): `api/routes/health.py
attr-defined -> issue #62 (settings.redis_host does not exist on Settings)`, with
`attr-defined` suppressed specifically on this module.

## Scope

**In scope:** the Redis probe in `api/routes/health.py` — build the Redis client from
the `redis_url` field that `Settings` actually defines, instead of the nonexistent
`redis_host`/`redis_port` fields. Remove the now-unneeded mypy `attr-defined` suppression
for this module in `pyproject.toml` (the repo's own convention, per `docs/CONTRIBUTING.md`:
"Fixing a seeded bug should remove its entry here"). Add a unit test covering the health
route's Redis probe so this regression is caught going forward.

**Not in scope:**
- The `postgres: unhealthy` result in the same response. That is a separate,
  pre-existing SQLAlchemy issue (`Textual SQL expression 'SELECT 1' should be explicitly
  declared as text('SELECT 1')`), noted in my Unit 2 repro as unrelated, and not part of
  what issue #62 describes.
- Issue #61, which the mypy-override comment names as "related" but which I have not
  reproduced or investigated; I am not touching whatever #61 covers.
- Any broader refactor of the health route (e.g., restructuring all three dependency
  checks, adding retries, changing the response shape). The route's other two checks and
  its overall shape are untouched.
- Any other caller of `Settings` (e.g. `agent/tools/market_analyzer.py`,
  `agent/memory/session_store.py`, `safety/monitoring.py`, `safety/rate_limiter.py`): a
  grep across the repo shows none of them reference `redis_host`/`redis_port` — they all
  take an already-constructed `redis_client` — so this fix touches no other call site.

## Files

- `api/routes/health.py` — the Redis probe (currently lines 40-58).
- `pyproject.toml` — remove the `api.routes.health` / `attr-defined` mypy override entry
  that exists only to suppress this seeded bug.
- `tests/unit/test_health.py` (new) — unit test(s) for the health route's Redis probe.

## Approach

1. In `api/routes/health.py`, replace the `redis.Redis(host=..., port=...)` construction
   with `redis.Redis.from_url(settings.redis_url, decode_responses=True)`, preserving the
   existing `r.ping()` call and the existing try/except/logging structure around it.
2. In `pyproject.toml`, remove the `[[tool.mypy.overrides]]` block whose `module =
   "api.routes.health"` disables `attr-defined`/`call-overload`/`index` for this seeded
   bug (re-run `make typecheck` after, to confirm nothing else in that module still needs
   those suppressions; if something does, narrow the override instead of dropping it
   wholesale — this is a named risk below).
3. Add `tests/unit/test_health.py`: following the existing `tests/unit/` convention of
   `unittest.mock.Mock`/`patch` for Redis clients (see `test_rate_limiter.py`), patch
   `redis.Redis.from_url` to assert the probe builds its client from `settings.redis_url`
   (not the removed `redis_host`/`redis_port` fields) and that a reachable Redis yields
   `dependencies.redis == "healthy"`; keep the test scoped to the Redis dependency only
   (not postgres/vector_db).

## Test plan

Before (from my Unit 2 reproduction, re-run against this same commit):
`GET /health` with Redis reachable returns HTTP `503`,
`"dependencies":{"redis":"unhealthy", ...}`, and the server log shows
`redis_health_check_failed error="'Settings' object has no attribute 'redis_host'"`.

After: re-run the identical steps (`docker compose up -d`; confirm `redis-cli ping` ->
`PONG`; `curl -s -w "\nHTTP_STATUS:%{http_code}\n" http://localhost:8000/health`).
Expected observable result: the response's `dependencies.redis` field reads `"healthy"`
(not `"unhealthy"`), and the `redis_health_check_failed` / `AttributeError` log line no
longer appears for that request. The pre-existing, out-of-scope `postgres: unhealthy`
result is expected to persist unchanged (confirming this fix is scoped to Redis only,
not accidentally touching the Postgres probe).

Additionally: the new `tests/unit/test_health.py` test(s) pass; `make typecheck` passes
with the `api.routes.health` mypy override removed; `make lint` and `make test-unit`
stay green (per `docs/CONTRIBUTING.md`'s CI requirements).

## Risks and unknowns

- I have not yet confirmed whether `api/routes/health.py`'s mypy errors are *only*
  `attr-defined` (from `redis_host`/`redis_port`) or whether `call-overload`/`index` (also
  suppressed in the same override block) depend on something this fix does not touch. If
  `make typecheck` still fails on this module after the fix, I will narrow the override to
  whatever specific line/code still needs it, rather than leaving the whole suppression in
  place.
- `tests/unit/test_rate_limiter.py` mocks Redis via dependency injection
  (`RateLimiter(mock_redis)`), but `health.py`'s route constructs its own client inline
  rather than taking one via DI. I plan to test this by patching `redis.Redis.from_url`
  at the point of use rather than changing the route's DI shape, since changing the
  route's signature is outside this issue's scope; if that patch target proves awkward
  once I'm actually writing the test, I will record the actual approach taken here.
- This is a genuinely small, isolated fix; I do not expect wider deviations, but will
  record any below if they occur.

## Deviations

Nothing changed; the plan held. The implementation matches what was posted:

1. `api/routes/health.py`'s Redis probe now builds its client with
   `redis.Redis.from_url(settings.redis_url, decode_responses=True)` instead of
   `redis.Redis(host=settings.redis_host, port=settings.redis_port, ...)`.
2. The `pyproject.toml` mypy override for `api.routes.health` had its `attr-defined`
   suppression removed; `call-overload` and `index` were kept, exactly as flagged as a
   risk in the plan, because they belong to the separate, out-of-scope issue #61 (the
   Postgres `"SELECT 1"` probe in the same file) — confirmed by reading #61 directly and
   by `mypy api/routes/health.py` passing clean with only `attr-defined` removed.
3. `tests/unit/test_health.py` was added with 3 tests (probe builds from `redis_url`;
   reports `healthy` when reachable; still reports `unhealthy` on a real failure, so the
   fix cannot mask a genuine Redis outage), using the repo's existing
   `unittest.mock`/`pytest.mark.asyncio` convention, patching `redis.Redis.from_url` at
   the call site rather than changing the route's dependency-injection shape (the
   DI-mismatch risk named in the plan resolved this way, without needing to touch the
   route's signature).

Before/after evidence, test results, and lint/typecheck output are recorded in this
unit's `plan-and-implement.md` write-up.
