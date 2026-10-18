# Test evidence: issue #62

Captured 2026-10-18. Branch `fix/62-redis-health-check`, commit `098046eb5a7ceca44a5b363184392a2c64bf3012`.
Base `upstream/main` and local `main`: `2f4e82f52efbcfcc57d65b3fa5348672163ca088`.
macOS, Apple Silicon; existing Python 3.12.0 environment requires `arch -arm64`
because this terminal runs under Rosetta. Docker server 29.2.1. No production keys
or services were used. Both route runs use the same Compose Redis.

## Reproduction before

Authentic historical Unit 2 evidence:
https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-5866677310

Fresh independent before run: detached upstream-main worktree at
`/tmp/ai301-unit4-20261018/base`; existing venv invoked with native ARM Python.
Started the real API with `DATABASE_URL=postgresql+asyncpg://pathreview:pathreview@localhost:5433/pathreview_dev REDIS_URL=redis://localhost:6379/0 LOG_LEVEL=DEBUG arch -arm64 /Users/rahul/Desktop/pathreview-ai301-fa26-s3/.venv/bin/python -m uvicorn api.main:app --host 127.0.0.1 --port 8014`.
Port 8014 isolates it from the branch API at port 8000; the route and Redis trigger are identical.

`docker compose up -d` started db, Redis and vector-db. Redis control:

```text
$ docker compose exec -T redis redis-cli ping
time="2026-10-18T03:20:45-07:00" level=warning msg="/Users/rahul/Desktop/pathreview-ai301-fa26-s3/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion"
PONG

EXIT_STATUS:0
```

```text
$ curl -sS -w '\nHTTP_STATUS:%{http_code}\n' http://127.0.0.1:8014/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-10-18T10:20:44.194440"}}
HTTP_STATUS:503

EXIT_STATUS:0
```

Corresponding server log:

```text
2026-10-18 03:20:44 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=e3678764-e37f-4e65-8793-91fec508b3ad
2026-10-18 03:20:44 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'" request_id=e3678764-e37f-4e65-8793-91fec508b3ad
2026-10-18 03:20:44 [debug    ] vector_db_health_check_passed  request_id=e3678764-e37f-4e65-8793-91fec508b3ad
```

## Reproduction after

Started the branch API with `arch -arm64 .venv/bin/uvicorn api.main:app --host 127.0.0.1 --port 8000` using the existing local .env; no .env contents copied here.

```text
$ curl -sS -w '\nHTTP_STATUS:%{http_code}\n' http://127.0.0.1:8000/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"healthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-10-18T10:20:30.603917"}}
HTTP_STATUS:503
```

Corresponding server log:

```text
2026-10-18 03:20:30 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=fba9319d-e773-4bd5-b52a-5862eb337984
2026-10-18 03:20:30 [debug    ] redis_health_check_passed      request_id=fba9319d-e773-4bd5-b52a-5862eb337984
2026-10-18 03:20:30 [debug    ] vector_db_health_check_passed  request_id=fba9319d-e773-4bd5-b52a-5862eb337984
```

Redis changes from unhealthy with AttributeError to healthy with
`redis_health_check_passed`. Overall HTTP 503 remains because of the separate
Postgres `SELECT 1` bug (#61); no full-health claim is made. The route's vector
check is a placeholder and its healthy field does not prove a real heartbeat.

## Repository checks

| Check | Observed result |
|---|---|
| `make test-unit` | Initial Rosetta run could not load ARM packages. `arch -arm64 make test-unit`: exit 0, 378 passed, 53 xfailed, 6 warnings. |
| `make test-integration` | Native ARM run collects 0 items; pytest exit 5, make exit 2. `tests/integration/` contains only `__init__.py`. CI explicitly accepts pytest exit 5 for this empty suite; this is not a passing integration suite. |
| `make lint` | Exit 0, All checks passed. |
| `make typecheck` | Rosetta could not load ARM mypy. Native ARM run exits 2 because installed NumPy stubs contain a Python 3.12 type statement while project mypy targets 3.11. The identical command on isolated upstream main produces the same error. |
| `make typecheck VENV_BIN=/tmp/ai301-unit4-20261018/venv311/bin` | Exit 0, Python 3.11 environment, unchanged project target; no issues in 76 source files. |
| `.venv/bin/mypy api/ core/ ingestion/ rag/ agent/ safety/ --python-version 3.12` (native ARM) | Exit 0, no issues in 76 source files. Supplemental result; does not replace the failing unmodified make command. |
| `arch -arm64 .venv/bin/black --check .` | Exit 0, 111 files unchanged. |
| `cd frontend && npm ci && npm test -- --run` | Dependencies installed; 2 test files, all 18 tests passed. Initial attempt before npm ci could not find vitest. |


## Regression coverage and seeded markers

Three executed unit tests cover configured URL construction (nondefault host,
port and db plus decode_responses=True), reachable Redis, and ping failure.
A search through tests found no #62 xfail marker; none was removed. The fix already
removes only `attr-defined` for api.routes.health, retaining the other baseline
suppressions for out-of-scope findings. The full suite's 53 unrelated xfails remain.

## Limitations and artifacts

Integration has no tests to execute. The native Python 3.12 environment's default
mypy target mismatch is disclosed above, with a same-base comparison and a clean
supplemental 3.12 run. A separate Python 3.11 environment now passes the unchanged project typecheck
target via `make typecheck VENV_BIN=/tmp/ai301-unit4-20261018/venv311/bin`: exit 0,
`Success: no issues found in 76 source files`. The existing venv was preserved.
Its initial install failed to build cryptography/libcst for x86_64 (Rust target);
upgrading pip in that separate venv and selecting available binary wheels
completed the install. No repository dependencies or config were changed. Screenshots are not applicable to this backend-only
change; the curl and request-correlated logs above are its demo.

Full local command logs: `/tmp/ai301-unit4-20261018/` (initial failures retained
in `*-initial.log`). Checks were run against the same tested file contents as the
commit above. AI assistance: the original Unit 3 implementation commit credits Claude Sonnet 5
as co-author. In Unit 4, Codex performed this inspection, test refinement,
command execution and evidence/PR drafting; Claude Code/Sonnet runs the course
precheck and evaluator. These are agent-executed checks, not a claim of separate
human execution or review.


## Python 3.11 follow-up

Using `VENV_BIN=/tmp/ai301-unit4-20261018/venv311/bin` with the existing Makefile:
- `make typecheck`: exit 0, no issues in 76 source files (project target remains 3.11).
- `make test-unit`: exit 0, 378 passed, 53 xfailed, 11 warnings in 15.34s.
- `make lint`: exit 0, All checks passed.
- `make test-integration`: 0 tests, pytest exit 5 / make exit 2, as in the existing environment.

The extra warnings include pre-existing deprecations and unawaited AsyncMock
coroutines; these are not counted as passes or failures beyond pytest's actual summary.


## Published PR and GitHub CI

PR: https://github.com/codepath/pathreview-ai301-fa26-s3/pull/249
All five jobs passed on head 098046e in
https://github.com/codepath/pathreview-ai301-fa26-s3/actions/runs/39119449876 .
Integration success uses the repository's explicit empty-suite exception; it does
not mean integration tests were executed. The PR CI checkbox was checked only
after all five job conclusions were observed as success.
