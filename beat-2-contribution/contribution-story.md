# Contribution: Clarify Python version requirements for contributors

**Contribution Number:** [1]
**Student:** Rahul Punji
**GitHub Username:** rahp124
**Issue:** https://github.com/Cog-Creators/Red-DiscordBot/issues/6458
**Pull Request:** https://github.com/Cog-Creators/Red-DiscordBot/pull/6814

## Toolkit

**Unit 5: Built and tested a profile-based issue scout**

On 2026-10-18, I migrated my preserved Unit 1 skill into the issue-scout structure, ran two metadata-based searches, and graded the top five: two were accepted and three rejected. The scan exposed a missing check for issues that may already be fixed, so I added a general still-actionable check and tested it against #288, #2, and #304; current source supported accepting #288 and #2 and rejecting #304. From the signed-in AI301 Issue Selection listing, the scout accepted Red-DiscordBot #6458, a documentation change outside my preferred bug/test work with some scope ambiguity, and rejected #6794 because four open PRs already address it; I chose #6458, posted a claim comment, and made the bounded documentation change.

**Unit 6: Added a voice guard and repository memory for the contribution**

I installed the voice guard, moved the voice guide to one canonical file, and added a local `CLAUDE.local.md` note with Red's contribution rules, the issue scope, and test commands. The first manual voice check flagged my generic claim-comment opening; I rewrote it to name issue #6458 and my action, and the checker passed before I updated the same comment. The unchanged Unit 1 regression evaluation scored 19/20: claimed 4/4, clear-accept 8/8, dead-repo 3/3, policy 1/1, and scope 3/4; I added a separate stale-attempt-history check to preserve behavior lost when the scope rubric changed.

**Unit 7: Installed the ship gate and ran its manifest on the live PR**

I installed the gate and ran its three manifest checks: documentation, style, and Python 3.8 tests all passed, then live `pr-precheck` accepted the branch and PR draft. The first run could not fetch the issue thread because the gate resolved legacy `gh 1.12.1`, which fails on GitHub's retired Projects API; putting my installed current CLI first on `PATH` fixed the environment, and the second full gate run passed. The wiring canary was stopped by the gate as expected.

**Unit 8: Installed the PR monitor and checked the post-ship state**

The monitor found PR #6814, 18 passing checks, a mergeable branch, and a review-required state. It saw Copilot's quota-limit notice as the only thread activity and drafted a wait note, not a reply or nudge; I checked the GitHub review and confirmed that it was bot-only. I did not post the draft, and the playbook sets a seven-day wait before considering one nudge.

---

## Why I Chose This Issue

My fit profile favored a bounded Python bug or regression test with a clear verification path and avoided broad refactors and infrastructure work. On the AI301 Issue Selection site, #6458 was listed in Cog-Creators/Red-DiscordBot; the scout accepted it because `CONTRIBUTING.md` says Python 3.8.1 or later while the `Makefile` defaults to `python3.8`, and `make newenv` gives a concrete way to verify the mismatch. This is documentation work rather than my preferred bug/test work. I chose it because the setup failure was reproducible and the change could remain limited to the contributor guide. Maintainer Flame442 had suggested either recommending 3.8.1 or documenting the full supported range, while calling the second option less realistic; that left some wording judgment, so I stated the oldest supported version and recommended it for development.

The second site-listed candidate, #6794, matched my bug-fix preference and had a clear reproduction, but the scout rejected it because four open PRs already targeted the issue. I claimed #6458 in a comment and then implemented the documentation correction. My scout initially lacked a current-actionability check; a targeted run against #288, #2, and #304 verified the new check, and the Unit 6 change later made scope difficulty a note instead of an automatic rejection while preserving stale attempt history as its own check. The direct grade accepted #6458 but identified the maintainer's unresolved wording choice as a caveat; I treated the existing guidance and observed Makefile behavior as enough to make a narrowly scoped clarification.

Claim comment: https://github.com/Cog-Creators/Red-DiscordBot/issues/6458#issuecomment-6254680971

**Toolkit**

- Tool: `issue-scout`, using my search strategy, evidence guide, and rubric to scan GitHub and grade the two site-listed candidates.
- What it produced: a five-issue scan, targeted actionability results, and direct grades accepting #6458 and rejecting #6794.
- Difficulties: the first rubric missed a possibly resolved issue and treated unresolved scope design as an automatic rejection; #6458 also has a maintainer wording choice that was not fully settled.
- What changed: added `still-actionable`, later changed the scope gate to `scope-feasible`, preserved stale attempt history separately, and kept the caveats in the decision record.

## Understanding the Issue

**Problem Description**

The contributor guide said Python 3.8.1 or newer was supported, but the default `make newenv` recipe invoked `python3.8`. A contributor with only a newer executable such as `python3` could follow the guide and still have setup fail because the Makefile could not find its default executable.

**Expected Behavior**

The guide should state the oldest supported Python release, recommend testing on that compatibility floor, and explain that `make newenv` uses `python3.8` unless the contributor selects another supported interpreter with the existing `PYTHON` variable.

**Current Behavior**

With only Python 3.12 on `PATH`, `make newenv` exited 2 with `make: python3.8: No such file or directory` and `make: *** [newenv] Error 1`.

**Affected Components**

`CONTRIBUTING.md` §4.1 contained the support guidance; the root `Makefile` sets `PYTHON ?= python3.8` and uses it in `newenv`. I changed the guide only; the Makefile behavior was already configurable and did not need a code change.

**Toolkit**

- Tool: GitHub CLI and direct source inspection of the issue, `CONTRIBUTING.md`, and `Makefile`.
- What it produced: confirmation of the mismatch and of the existing `PYTHON` override.
- Difficulties: the issue had multiple possible wording directions, and my older GitHub CLI failed while querying the issue because of a retired Projects API field.
- What changed: I kept the fix to one paragraph in the guide and used the installed current GitHub CLI for thread inspection.

## Reproduction Process

**Environment Setup**

I used macOS, the Red-DiscordBot `V3/develop` checkout at base commit `1531fc37`, Python 3.12 for the restricted-path reproduction, and Python 3.8.8 for the working setup. From the repository, `make newenv` installs the development environment under `.venv`; the repository's `CONTRIBUTING.md` describes this setup. The first attempt with only Python 3.12 available failed exactly at the Makefile's default `python3.8` invocation. Running the same setup with Python 3.8.8 available succeeded.

**Steps to Reproduce**

1. Check out Red-DiscordBot and make sure the Makefile and `CONTRIBUTING.md` are present.
2. Restrict `PATH` to Python 3.12 and the standard system directories, then run `/usr/bin/make newenv`.
3. Observe exit status 2 and `python3.8: No such file or directory`; make Python 3.8.8 available and run `make newenv` again, which completes successfully.

**Reproduction Evidence**

- **Commit showing reproduction:** There is no separate reproduction commit for this documentation issue. The implementation commit is [d0576ff](https://github.com/rahp124/Red-DiscordBot/commit/d0576ff04e8db2ae4bbf42b3a04bfd928a07ba80); the reproduction is the command and observed output recorded here.
- **Screenshots/logs:** No screenshot was needed for a command-line setup issue. The captured before transcript was: `make newenv` → exit 2; stdout `python3.8 -m venv --clear .venv`; stderr `make: python3.8: No such file or directory` and `make: *** [newenv] Error 1`. The after `make newenv` transcript ends with successful installation of Red and its development requirements.
- **My findings:** The failure was caused by the documented support range and the default executable not being connected clearly for contributors. The existing Makefile override made a documentation-only correction sufficient.

**Toolkit**

- Tool: `make newenv` with controlled `PATH` values and captured terminal output.
- What it produced: an exit-2 reproduction with Python 3.12 alone, followed by successful environment setup with Python 3.8.8.
- Difficulties: the system `make` invoked the fixed `python3.8` executable, so the first run could not start a virtual environment.
- What changed: I used the actual supported Python 3.8.8 executable for the successful after run and preserved both outputs locally.

## Solution Approach

**Analysis**

`CONTRIBUTING.md` §4.1 described Python 3.8.1 or newer, and the Makefile already provided `PYTHON ?= python3.8` plus `make PYTHON=/path/to/python newenv` as an override. The problem was missing contributor-facing explanation of the default and the compatibility-floor recommendation, not a broken Makefile recipe.

**Proposed Solution**

Clarify that Python 3.8.1 is the oldest supported version and recommend it for development so newer syntax is caught early. Explain the default `python3.8` executable and show how to use the existing `PYTHON` override. Leave setup behavior and application code unchanged.

**Implementation Plan**

1. Update `CONTRIBUTING.md` §4.1 with the oldest supported version and development recommendation.
2. Document the Makefile's `python3.8` default and existing `PYTHON` override after the `make newenv` instruction.
3. Re-run the setup reproduction and the repository's docs check; inspect the final diff and PR template.

**Deviations**

The PR plan was not posted as a separate issue comment before implementation; I had already made the change before learning Unit 6's plan-comment practice. The fix itself stayed within the stated documentation scope. The Unit 7 gate's first attempt was blocked by the legacy `gh` executable in its environment; I corrected `PATH` and reran the entire gate successfully. The gate also noted that I inspected, but did not execute, the alternate `PYTHON=` override command, and the PR makes no claim that it was run.

**Toolkit**

- Tool: Red's contributor docs and Makefile, plus local plan notes and the installed `ship-gate`.
- What it produced: a one-file, three-insertion/one-deletion documentation diff and a gate pass tied to the branch and PR draft.
- Difficulties: I had no pre-build plan comment, and the first gate run could not fetch the issue thread through legacy `gh`.
- What changed: I recorded the plan timing as a deviation and reran the gate with the current authenticated CLI first on `PATH`.

## Testing Strategy

The observable regression check was the setup command from the report. Before the change, with only Python 3.12 on `PATH`, `/usr/bin/make newenv` exited 2 because `python3.8` was missing. Afterward, `make newenv` with Python 3.8.8 available completed and installed Red plus its development requirements. The docs-only patch did not require an application unit-test change. I ran `tox -e docs` locally; HTML, six doctests, and Markdown documentation built successfully. The Unit 7 manifest then ran `.venv/bin/tox -e docs`, `.venv/bin/tox -e style`, and `.venv/bin/tox -e py38`; all passed. GitHub CI completed with 18 passing checks, including Tox Docs, Tox Style, Python 3.8–3.11 tests, Postgres 3.8–3.11, CodeQL, and ReadTheDocs. The repository's `tox -e py38` test suite was therefore run by the gate and CI, though the code change is documentation only.

**Toolkit**

- Tool: `make newenv`, `tox -e docs`, the Unit 7 manifest runner, and GitHub Actions.
- What it produced: before/after setup results; successful local docs build; three passing local gate checks; and 18 passing PR checks.
- Difficulties: the first gate run reached all three checks but could not read the issue thread because `gh 1.12.1` failed; the checks themselves passed.
- What changed: I put the current `gh` binary first on `PATH` and reran the full gate, which passed all checks and live review.

## Implementation Notes

**2026-10-18**

The issue-scout scan and direct grading identified #6458 as an accepted, reproducible docs mismatch; #6794 was rejected because four open PRs already targeted it. I posted a claim comment on #6458, reproduced the `make newenv` failure with Python 3.12 alone, and confirmed the setup succeeds with Python 3.8.8. I updated only `CONTRIBUTING.md`, ran `tox -e docs`, and opened PR #6814 against `V3/develop` from my fork branch.

**2026-10-18**

The voice checker rejected my original generic claim-comment opening, so I revised it to name the issue and my action; it passed before I updated the comment. The Unit 6 full regression evaluation scored 19/20 after I restored stale-attempt history as an independent rubric check. The gate's initial live review was unclear because legacy `gh` could not fetch issue evidence; with the current CLI first on `PATH`, docs, style, and Python 3.8 tests passed and `pr-precheck` accepted the package. The PR monitor found only an automated Copilot quota notice, so it drafted a wait note and I posted nothing.

**Toolkit**

- Tool: issue-scout, voice-guard, `CLAUDE.local.md` repository notes, ship-gate, and pr-monitor.
- What it produced: issue grades, a checked claim comment, a local repository guide, a passing gate transcript, and a read-only PR-state report with a wait-note draft.
- Difficulties: the scout needed a current-actionability check, the voice guide caught a generic opening, the gate initially found legacy CLI behavior, and the monitor surfaced a bot-only quota notice as thread activity.
- What changed: I refined the scout rubric, rewrote the claim comment, corrected the gate's `PATH`, and followed the monitor's instruction to wait rather than reply or nudge.

## Pull Request

**PR Link:** https://github.com/Cog-Creators/Red-DiscordBot/pull/6814

**PR Description:** The PR uses the repository template headings, “Description of the changes” and “Have the changes in this PR been tested?” It references `Fixes #6458`, explains the Python 3.8.1 recommendation and `python3.8`/`PYTHON` behavior, includes an AI-use disclosure, and reports the reproduction and docs test. It answers “Yes” to the template's test question. No screenshot is included because this is a documentation and terminal-reproduction change; the text diff and command output are the relevant evidence.

**Maintainer Feedback:**

- 2026-10-18: Copilot's automated review could not run because the requesting user's quota was reached; no human maintainer feedback has arrived.
- 2026-10-18: The PR monitor classified the bot-only activity as no human review and drafted a wait note. I did not post a response or nudge.

**Status:** Awaiting review. The branch is mergeable, 18 checks pass, and GitHub reports `REVIEW_REQUIRED` with merge state `BLOCKED` pending review.

**Toolkit**

- Tool: GitHub CLI for opening and checking the PR, the installed `pr-precheck` through the ship-gate, and pr-monitor for post-ship state.
- What it produced: PR #6814 against `V3/develop`, an accepted live precheck, 18 passing checks, and a draft wait note.
- Difficulties: Copilot's automated review was unavailable due to quota, and the gate initially used the legacy CLI for issue evidence.
- What changed: I reran the gate with current `gh`; I left the PR open and made no unneeded reply while waiting for human review.

## Learnings and Reflections

**Technical Skills Gained**

I traced a contributor setup mismatch across prose and a Makefile variable, used a restricted `PATH` to reproduce executable lookup failure, and verified both the default interpreter path and docs build. I also learned to make a local pre-PR gate run repository checks in a fresh shell and to check the CLI version used by subprocesses when API queries fail.

**Challenges Overcome**

The setup failure depended on which Python executable was available, so I captured the failing environment and then repeated setup with Python 3.8.8. The gate initially could not inspect the issue thread because it resolved `gh 1.12.1`; adding the installed current CLI to the front of `PATH` allowed a full rerun and an accepted verdict. The voice checker also caught that my first claim comment did not name the specific issue and action, which I corrected before updating the comment.

**What I'd Do Differently Next Time**

I would write and post the bounded plan before building, record the exact runtime version alongside each terminal transcript, and check which CLI binary will be inherited by automated tools before the first gate run. I would also separate automated bot activity from human review status when reading a new PR thread.

## Resources Used

- [Red-DiscordBot issue #6458](https://github.com/Cog-Creators/Red-DiscordBot/issues/6458), including maintainer Flame442's 2024 wording guidance.
- [Red-DiscordBot CONTRIBUTING.md](https://github.com/Cog-Creators/Red-DiscordBot/blob/V3/develop/CONTRIBUTING.md), especially setup and test instructions.
- [Red-DiscordBot Makefile](https://github.com/Cog-Creators/Red-DiscordBot/blob/V3/develop/Makefile), for the `PYTHON` default and `newenv` recipe.
- [PR #6814](https://github.com/Cog-Creators/Red-DiscordBot/pull/6814), including the project checks and current review status.
