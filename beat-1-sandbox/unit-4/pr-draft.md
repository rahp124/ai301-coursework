fix(api): use configured Redis URL in health probe

## Summary

The Redis health probe reads settings.redis_host/settings.redis_port, which Settings does not define, so it reports Redis unhealthy before trying to connect. Build the client from settings.redis_url so the probe reports actual Redis reachability. This is my AI301 course contribution for #62.

## Issue

Closes #62

Plan (posting mistake corrected transparently on 2026-10-18): https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-5983283508

The original issue comment contained only literal `@comment.md`; the intended Unit 3 plan was recorded in my course repo but was not visible in that issue comment until this dated correction. I am not claiming it was posted in full before implementation. Course plan record: https://github.com/rahp124/ai301-coursework/blob/main/beat-1-sandbox/unit-3/plan.md
Reproduction: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-5866677310

## Changes

- Use `redis.Redis.from_url(settings.redis_url, decode_responses=True)` in the existing probe; preserve ping, logging and failure handling.
- Remove only the health module's matching `attr-defined` mypy suppression.
- Add three unit tests for configured URL construction, reachable Redis and ping failure. The URL assertion uses a nondefault URL to catch hardcoded defaults.

## Testing

- [x] **CI is green on this PR (all five jobs)** — [verified successful run](https://github.com/codepath/pathreview-ai301-fa26-s3/actions/runs/39119449876). Integration uses the repo’s explicit no-tests-collected exception.
- [x] Unit tests pass (`make test-unit`) — native ARM invocation: 378 passed, 53 xfailed.
- [ ] Integration tests pass (`make test-integration`) — 0 tests collected; pytest exits 5, make exits 2. CI explicitly treats the empty suite's exit 5 as success.
- [x] Linter passes (`make lint`).
- [x] Type checker passes (`make typecheck VENV_BIN=/tmp/ai301-unit4-20261018/venv311/bin`) — clean separate Python 3.11 environment, unchanged project mypy target, no issues in 76 source files. The original Python 3.12 venv default command fails on a NumPy stub syntax/3.11 target mismatch, also reproduced on unchanged main; supplemental `--python-version 3.12` passes there.
- [x] New/updated tests cover the changes.
- [x] If this fixes a seeded bug: removed its matching suppression in `pyproject.toml`; no corresponding #62 xfail marker exists in this checkout (searched tests), so none was removed.

Separate Python 3.11 follow-up: `make test-unit VENV_BIN=/tmp/ai301-unit4-20261018/venv311/bin` also passes (378 passed, 53 xfailed, 11 warnings); lint passes and integration again collects 0 tests.

Black check: 111 files unchanged. Frontend: `npm ci`, then `npm test -- --run`: 18 passed in 2 files.
Initial Rosetta runs could not load the existing ARM Python packages; checks were rerun natively with `arch -arm64`. No dependency or CI config changes are included.

## Screenshots / Demo

Backend-only change; no UI screenshots apply. Fresh real API before/after on the same Compose Redis (`redis-cli ping` -> `PONG`):

Before, isolated upstream main `2f4e82f`, port 8014:
```text
$ curl -sS -w '\nHTTP_STATUS:%{http_code}\n' http://127.0.0.1:8014/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-10-18T10:20:44.194440"}}
HTTP_STATUS:503

EXIT_STATUS:0
```
```text
2026-10-18 03:20:44 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=e3678764-e37f-4e65-8793-91fec508b3ad
2026-10-18 03:20:44 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'" request_id=e3678764-e37f-4e65-8793-91fec508b3ad
2026-10-18 03:20:44 [debug    ] vector_db_health_check_passed  request_id=e3678764-e37f-4e65-8793-91fec508b3ad
```

After, branch `098046e`, port 8000:
```text
$ curl -sS -w '\nHTTP_STATUS:%{http_code}\n' http://127.0.0.1:8000/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"healthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-10-18T10:20:30.603917"}}
HTTP_STATUS:503
```
```text
2026-10-18 03:20:30 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=fba9319d-e773-4bd5-b52a-5862eb337984
2026-10-18 03:20:30 [debug    ] redis_health_check_passed      request_id=fba9319d-e773-4bd5-b52a-5862eb337984
2026-10-18 03:20:30 [debug    ] vector_db_health_check_passed  request_id=fba9319d-e773-4bd5-b52a-5862eb337984
```

Redis flips to healthy and its AttributeError disappears. Overall HTTP 503 remains because the separate Postgres probe bug (#61) is outside this change.

## Notes for Reviewers

Please review the Redis probe and its regression tests. The original Unit 3 fix commit is preserved; the follow-up commit strengthens the configured-URL assertion. Unrelated health-route suppressions and seeded xfails remain. `plan.md`, `comment.md`, `pr_draft.md` and `test_evidence.md` are local coursework review artifacts and are excluded from this branch's commits.

AI assistance: the original Unit 3 implementation commit credits Claude Sonnet 5 as co-author. In Unit 4, Codex inspected the repositories, refined the regression assertion, executed checks and reproduction, and drafted this PR and evidence. Claude Code/Sonnet executes the course's rubric-based precheck and eval harness. These are agent-executed results; I am not claiming separate human execution or review.

## Deviations

The plan anticipated narrowing the mypy override if unrelated codes remained, so `call-overload` and `index` are retained. Unit 4 strengthened the existing URL test to exercise nondefault configuration and assert `decode_responses=True`; production scope is unchanged. The fresh before run uses a detached main worktree and port 8014 to preserve branch work. Integration's empty suite and the original local venv typecheck mismatch are disclosed (typecheck passes in the separate Python 3.11 venv) rather than reported as passes.
