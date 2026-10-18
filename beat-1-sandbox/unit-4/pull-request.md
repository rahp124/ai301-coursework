# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/249

**Branch**

`fix/62-redis-health-check` on `rahp124/pathreview-ai301-fa26-s3`, targeting
`codepath/pathreview-ai301-fa26-s3:main` (base `2f4e82f`). Issue #62 is verified
from my Unit 2 reproduction, Unit 3 posted plan, local plan and the live issue.
Existing implementation commit `34765cb` is preserved; Unit 4 commit `098046e`
strengthens the configured-URL assertion. No branch history was rewritten.

## Eval iterations

**Run history**

1. Initial full run, installed skill, Sonnet, 20 scored packages,
   `python3 run_eval.py --skill /Users/rahul/.claude/skills/pr-precheck --save-run eval-run.txt --out initial-results.json`:
   **20/20 agreement, PASS**. Category coverage: clear-accept 7/7,
   not-tested 4/4, silent-drift 4/4, standards-wall 2/2, unreviewable 3/3.
   Preserved verbatim in `eval-initial.txt`.
2. Final confirming full run, same installed skill and no rubric revisions,
   `python3 run_eval.py --skill /Users/rahul/.claude/skills/pr-precheck --save-run eval-run.txt --out final-results.json`:
   **20/20 agreement, bar 18/20: PASS**. Category coverage: clear-accept 7/7,
   not-tested 4/4, silent-drift 4/4, standards-wall 2/2, unreviewable 3/3.
   The untouched harness-written output is `eval-run.txt`; per-check results
   are `eval-results.json`.

No smoke run or targeted retry occurred: the initial full run had no disagreements,
so there was nothing to repair with `--only`. No checks were loosened; the final full
run checks stability with the identical four grading-file fingerprints. Packages,
gold labels and the supplied harness were not modified.

**Package analysis**

`pkg-13` (source `neovim/neovim#41337`), gold `accept`, rubric `accept` in both full
runs. The package fixes cmd.exe metacharacter parsing in Windows `vim.ui.open`.
The plan explicitly defers percent-sign environment expansion. Its description
says: "percent-sign environment expansion (`%VAR%`) is NOT handled here" and
"The plan's deferral note covers it". The diff escapes `& | ^ < >`, with a relevant
regression test. Observable evidence shows the original truncated URL / exit 1
changing to the complete URL / exit 0, plus a passing UI functional spec.
The tool accepts that honestly bounded omission rather than silently expanding the
plan into full Windows URL handling. The Problem/Solution content and conventional
commit satisfy the stated repository requirements; no AI disclosure policy is stated
in that bundle. This result follows the frozen evidence, without fetching the source
issue or using the package id as a grading rule.

**Check rationale**

From submitted `tools/pr-precheck/rubric.md`, the `plan-fidelity` pass condition,
quoted exactly:

> The diff delivers the plan's bounded behavior and named deliverables, or the plan's deviation notes explicitly explain each addition, omission or changed approach. Description claims match the implementation. Ordinary supporting tests and necessary bookkeeping fit the boundary. Silent additions, silent omissions, or claims contradicted by the diff fail.

I adapted the Unit 3 pattern of named evidence and required/preferred checks to a
PR: compare actual diff hunks to deliverables before believing the description.
I rejected an exact-file-list or 'no deviations allowed' rule, which would confuse
honest implementation learning with silent drift. Requiring the plan's deviation
notes to account for additions and omissions preserves the boundary while allowing
bounded changes. I also kept testing, reviewability and repository requirements as
separate gates: an honestly disclosed omission does not excuse generated debris,
a required disclosure omission, or a newly introduced failing check. This wording
was written before the first full run and did not change after either run.

**Trade-offs**

The deviation allowance accepts the documented percent-sign limitation in `pkg-13`;
a rule demanding every related edge case would reject it despite its scoped plan.
It could miss a bad decision that is thoroughly documented and not contradicted by
the provided evidence: this is a readiness check against the package's accepted plan,
not a substitute for redesigning that plan. Observable-testing and repository checks
still gate unsupported claims. Allowing explicitly reasoned check omissions also
means this skill can accept a PR whose external CI is still pending; the PR must not
claim green CI, and the repository's final CI requirement remains outstanding until
observed. No loosening happened, so no category canary retry was needed. Both complete
runs matched all four silent-drift packages and both standards-wall packages, with
identical grading-file fingerprints: the honest-outcome allowance did not erase those
failure families in the observed runs. This evidence is bounded to these actual runs,
not a claim that the model will never vary.

## Actual change validation

`test-evidence.md` records fresh real API before/after evidence and all check outcomes;
`checks/` preserves full command output, including default typecheck failures and
same-base evidence. Native ARM Python 3.12: 378 passed / 53 xfailed; lint, Black and
18 frontend tests passed. Separate Python 3.11 environment: unchanged Makefile
`make typecheck VENV_BIN=...` passes (76 source files), unit tests 378 passed / 53
xfailed, lint passes. Both environments collect no integration tests (pytest exit 5,
make exit 2), matching CI's documented empty-suite exception. The original venv's
NumPy-stub/3.11-target error is reproduced on untouched upstream main; the clean
Python 3.11 run resolves that environment problem without changing the repo.

Before: Redis answers PONG but /health reports redis unhealthy with missing
redis_host. After: redis healthy and redis_health_check_passed. HTTP 503 persists
because of separate Postgres issue #61. No #62 xfail exists to remove; only its
attr-defined suppression is removed. Unit 4's nondefault-URL test refinement is
recorded in the Unit 3 plan's Deviations. Local review markdown is excluded from
the Path Review commits.

## Live precheck and submission record

Initial actual Claude `/pr-precheck` live run returned `accept` with all required
checks passing. It flagged displayed curl quoting and incomplete AI attribution;
both were corrected before the final live run. The original implementation commit
credits Claude Sonnet 5; Unit 4 inspection, test refinement, execution and drafting
used Codex. Claude Code/Sonnet executed the supplied eval harness and live skill.
No claim of independent human execution or review is made.

Official materials: `codepath/ai301-unit4-starter`, including unchanged skill contract
and eval README; the user's detailed assignment requirements were followed. No
separate attachment was visible in this session, so unseen portal-only requirements
have not been verified. Portal submission itself remains a student action.


### Upstream plan-record correction

The second live run returned accept but found that the Unit 3 plan URL pointed to
a comment containing only literal `@comment.md`. A direct GitHub API read confirmed
that; the Unit 2 reproduction comment was checked and contains real evidence.
After explicit user authorization, the original plan comment was updated in place
with a dated explanation, the intended plan and current Unit 4 results. Its URL
remains unchanged. The correction was re-fetched and compared byte-for-byte to the
prepared body. The PR now discloses the original gap and links both the corrected
issue comment and the contemporaneous course plan. Unit 3's record is annotated
so it does not imply the full plan was visible on the issue before the correction.


Local `git diff --check` reports trailing whitespace in the copied harness table.
The harness transcript was intentionally left byte-for-byte unchanged, as required;
this is generated output rather than a hand-formatted source file.


### Final live checks and opened PR

Three actual Claude Code/Sonnet live runs occurred in order:
1. Initial live run: accept; corrected command display and original Claude attribution.
2. Second live run: accept; discovered literal `@comment.md` in the linked plan comment.
3. Final submission live run, after the explicitly authorized correction: accept,
   all six checks pass. The fixed fenced JSON is last in `pr-precheck-live.txt` and
   parsed in `pr-precheck-verdict.json`. The stream trace confirms live scope was
   read first and the installed SKILL.md, voice guide, rubric, procedure and evidence
   guide were read. Invocation/prompt and hashes are preserved alongside the result.
   The reviewer did not independently rerun tests; the primary agent executed and
   captured the checks recorded above. The reviewer attempted an unallowed test
   command but graded the existing evidence; no additional test result is claimed.

The final review's remaining reservations are disclosed: CI must finish after
opening; integration has no tests; the original venv mismatch persists while the
separate Python 3.11 typecheck passes; the plan wasn't fully visible on the issue
before implementation. The archived course plan commit (2026-10-04 19:45Z) follows
the fix commit (19:06Z); calling it a contemporaneous course record does not establish
a pre-implementation publication timestamp. No such claim is made. A stale illustrative
#62 comment in pyproject.toml is optional baseline commentary, not an active suppression,
and is left alone to keep the code diff focused.

The branch was pushed normally to the fork, then the PR was created after duplicate
checks, with base `codepath/pathreview-ai301-fa26-s3:main` and head
`rahp124:fix/62-redis-health-check` at `098046e`. PR head/base were verified from
the creation response. No local review markdown was staged in Path Review.


### Final CI and portal handoff

All five jobs passed on PR #249 at head `098046e`:
[CI run 39119449876](https://github.com/codepath/pathreview-ai301-fa26-s3/actions/runs/39119449876).
`lint`, `typecheck`, `test-unit`, `test-integration`, and `frontend` all have observed
`success` conclusions; `ci-status.json` records their individual links and completion
times. The integration job uses the repository's explicit empty-suite exception,
not an executed integration suite. The PR's CI checkbox was left unticked before
opening and checked only after these outcomes were observed; the published PR body
was then re-fetched and verified. `pr-draft.md` now mirrors that final body (title first).

Submission repository: https://github.com/rahp124/ai301-coursework .
The student still needs to submit that repository URL in the Unit 4 course portal
and include the PR URL if the portal asks. No portal submission was performed, and
no claim is made about additional instructions in the attachment that was not visible
in this session. The historical plan-posting gap is disclosed above; correcting it
today does not establish that it was posted upstream before implementation.
