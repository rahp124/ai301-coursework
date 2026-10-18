Correction on 2026-10-18: this comment previously contained only the literal `@comment.md`, not the intended plan text. During Unit 4 I discovered the posting mistake. The intended Unit 3 plan is reproduced below for transparency; I am not claiming the full text was visible here before today. The contemporaneous course record is https://github.com/rahp124/ai301-coursework/blob/main/beat-1-sandbox/unit-3/plan.md .

## Intended Unit 3 plan

Following up on my reproduction above with a plan for issue #62.

Diagnosis: the Redis probe in `api/routes/health.py` builds its client from
`settings.redis_host`/`settings.redis_port`, but `Settings` only defines `redis_url` — no
`redis_host`/`redis_port` fields exist. That raises an `AttributeError`, which the probe's
bare `except Exception` catches, so it reports `redis: unhealthy` even when Redis is
actually reachable (as my reproduction's `redis-cli ping` -> `PONG` confirmed, alongside
the exact `'Settings' object has no attribute 'redis_host'` log line).

Plan: build the Redis client from `settings.redis_url` (via `redis.Redis.from_url`)
instead of the nonexistent host/port fields, remove the `api.routes.health`
`attr-defined` mypy suppression in `pyproject.toml` now that the underlying bug is fixed
(per this repo's convention for seeded bugs), and add a unit test for the health route's
Redis probe so this doesn't regress. This is scoped to the Redis probe only — the
separate `postgres: unhealthy` result in the same response is a pre-existing, unrelated
issue I noted in my repro and am not touching here, and I'm not touching issue #61 either,
which the mypy-override comment names as related but which I haven't reproduced myself.

Test plan: re-run my Unit 2 repro steps against the fix — same `docker compose up -d` +
`curl /health` — and confirm `dependencies.redis` now reads `"healthy"` instead of
`"unhealthy"`, with the `redis_health_check_failed` log line gone for that request. Plus
the new unit test passing and `make typecheck`/`make lint`/`make test-unit` staying green.

Open question I'm flagging rather than assuming: the current mypy override for this
module also suppresses `call-overload` and `index`, not just `attr-defined` — I'll check
after the fix whether those are still needed for something unrelated, and narrow the
override instead of removing it outright if so.

I'll post the branch and results once I've built and tested this.


## Unit 4 status (2026-10-18)

The existing implementation is on `rahp124:fix/62-redis-health-check` (original fix `34765cb`, test refinement `098046e`). The probe uses `redis.Redis.from_url(settings.redis_url, decode_responses=True)`, removes the matching `attr-defined` suppression, and adds three regression tests. The follow-up test sets a nondefault URL rather than hardcoding the default. Other probes and unrelated suppressions are unchanged.

A fresh detached-main reproduction reports Redis unhealthy with the missing-attribute error despite `PONG`; the fixed branch reports Redis healthy. Both still return HTTP 503 because of the separate Postgres bug (#61). Unit tests: 378 passed, 53 unrelated xfailed; lint, Black and 18 frontend tests pass. Typecheck passes with the unchanged project target in a separate Python 3.11 venv; the original Python 3.12 environment's NumPy-stub mismatch is recorded. Integration collects no tests (pytest exit 5), as expected by CI's empty-suite exception. CI on the PR is not yet claimed green.

AI assistance: the original fix commit credits Claude Sonnet 5. Codex helped inspect the records, refine the URL assertion, run checks/reproduction and draft this correction; Claude Code/Sonnet ran the course eval and live precheck. These are agent-executed checks, with no claim of separate human execution or review.
