# Toolkit Portfolio

**Student:** Rahul Punji

## Featured Tools

I chose issue-scout, repro-check, plan-check, and pr-precheck because they cover the decisions I kept having to make: whether an issue was worth starting, whether I had reproduced the right problem, whether my plan followed the evidence, and whether my PR was ready for review. Their most useful changes came from specific mistakes. The scout needed to recognize work that was already resolved, while the reproduction and plan checkers needed to distinguish incomplete evidence from evidence that contradicted a claim.

**issue-scout**

**What it is, and the judgment hole I wrote:** A profile-based GitHub issue finder and grader, migrated from my Unit 1 issue-select skill. My contribution is the search strategy and rubric: what counts as bounded, unclaimed, still actionable work, and which difficulties should be reported for me to weigh. My profile favored Python bugs and regression tests, but it did not make documentation work an automatic rejection.

**How it evolved, dated:** On September 20, 2026, issue-select's early full runs scored 13/20 and 15/20. I revised `bounded-scope` because it was rejecting several related files or causes serving one goal as if they were independent tasks. A targeted run reached 5/5, followed by a final 19/20 full run. On October 18, I migrated it to issue-scout. The scan exposed a missing check for possibly resolved reports, so I added `still-actionable` and checked current source for three candidates. The Unit 6 scope change then moved design uncertainty into a difficulty note; I preserved the old age-and-abandoned-PR rule separately as `stale-attempt-history`.

**Evidence:** My October 18 scan notes record the targeted actionability results:

> Results: #288 passes `still-actionable`; #2 passes it; #304 fails it and remains rejected (also fails `bounded-scope` and `not-blocked`).

The saved #304 grade gives the deciding source evidence:

```text
main redis util.py `_extract_conn_attributes` now uses
conn_kwargs.get('host','localhost')/get('port',6379) and handles 'path',
so the reported KeyError no longer occurs
```

These excerpts come from my installed scout's `scan-notes.md` and `scan-output.md`. In my actual contribution selection, the scout accepted Red-DiscordBot #6458 and rejected #6794 because four open PRs already addressed it. I chose #6458 and opened documentation PR #6814. Coursework commits `790e7eb` and `20c9ba9` record the scan and candidate grading on October 18; the contribution story records my final choice. The Unit 6 regression record still reports 19/20, so I do not treat the new checks as proof of perfect grading.

**repro-check**

**What it is, and the judgment hole I wrote:** A checker for a claim comment and reproduction report. I wrote the rubric, scope, evidence guide, and voice guide. The important judgment is whether the attempt targets the issue's actual trigger and whether the conclusion stays within what the output shows. An evidenced inability to reproduce can pass; a successful run against an unrelated trigger cannot.

**How it evolved, dated:** On September 28, 2026, my first full evaluation scored 20/20, but a confirming run dropped to 19/20. The checker rejected `pkg-03` because I had confused a human-authorship policy with an explicit AI-disclosure requirement. I narrowed `disclosure-when-required` to an actual disclosure request, then reran the disagreement with disclosure and clear-accept canaries. That targeted run scored 5/5, and the final full run returned 20/20.

**Evidence:** The saved Unit 2 harness output contains:

```text
categories: clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4
agreement: 20/20 scored items  (bar: 18/20: PASS)
```

My real reproduction of Path Review #62 also shows why trigger fidelity mattered:

```text
redis-cli ping -> PONG
HTTP_STATUS:503
redis_health_check_failed error="'Settings' object has no attribute 'redis_host'"
```

The same response reported Postgres unhealthy. My report separated that error from the Redis configuration mismatch instead of treating every failure as part of #62. The reproduction is recorded in [my issue comment](https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-5866677310); coursework commit `edb04d6`, dated September 28, preserves the rubric and evaluation record. The reproduction output establishes the bug; the evaluation sequence establishes the checker revision.

**plan-check**

**What it is, and the judgment hole I wrote:** A checker for whether a diagnosis, proposed change, and test plan are ready to build from. I wrote the rubric and procedure around evidence consistency, bounded scope, an executable approach, and a test with an observable outcome. I wanted it to allow a clearly identified inference while rejecting a cause that a control run or trace actually rules out.

**How it evolved, dated:** On October 4, 2026, the initial full run scored 19/20. It rejected `pkg-14` because my conditions demanded proof of every diagnostic detail and the exact function to edit. I changed `diagnosis-grounded` to distinguish a reasonable, flagged inference from a contradicted cause, and changed `executable` to accept a concrete target area and mechanism before the precise function was pinned down. A ten-package targeted run held all four wrong-cause rejects and all three unbuildable rejects, and the final full run scored 20/20.

**Evidence:** The saved Unit 3 evaluation reports:

```text
categories: clear-accept 7/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4
agreement: 20/20 scored items  (bar: 18/20: PASS)
```

My #62 plan named a specific outcome: rerun the original health request and expect `dependencies.redis` to become healthy. The actual before/after record was:

```text
Before: postgres=unhealthy, redis=unhealthy, vector_db=healthy; HTTP 503
After:  postgres=unhealthy, redis=healthy,   vector_db=healthy; HTTP 503
```

This is a compact excerpt of the response fields, not the full JSON. It shows that the planned Redis outcome happened while the separate Postgres problem remained. Coursework commit `a338390`, dated October 4, preserves the plan, implementation record, and evaluation. The later upstream check found that my linked plan comment contained only literal `@comment.md`; the full comment was corrected on October 18. I cannot use the local plan as evidence that it was published upstream before implementation.

**pr-precheck**

**What it is, and the judgment hole I wrote:** A readiness review of a branch, PR description, plan, test evidence, and repository requirements. I wrote the rubric and review procedure to compare substantive diff hunks with the plan, require observed test outcomes, and distinguish disclosed limitations from unsupported success claims. A documented deviation can pass, but it does not excuse an introduced failure or a missing hard requirement.

**How it evolved, dated:** On October 18, 2026, both full evaluations scored 20/20 with the same grading files. I did not revise the rubric between runs. Its practical value came from the live reviews: the first flagged broken command formatting and inconsistent AI attribution; the second found the linked plan comment's literal `@comment.md`. I corrected the artifacts, disclosed the plan-posting gap, and obtained a final accepting review before opening PR #249. This tool changed less than the other featured tools because the observed evaluations gave me no disagreement to repair.

**Evidence:** The second live review included this finding:

```text
Tension: posted plan comment 5983283508 body is literally '@comment.md'.
```

That is a catch from my own work, preserved in `pr-precheck-second.txt`, rather than an evaluation fixture. The October 18 coursework commit `edbf20f` records the correction and final review. After opening [PR #249](https://github.com/codepath/pathreview-ai301-fa26-s3/pull/249), all five CI jobs succeeded at head `098046e`: lint, typecheck, test-unit, test-integration, and frontend. The integration job used the repository's empty-suite exception; it did not execute integration tests. The initial and final 20/20 evaluations establish observed consistency, not a rubric improvement.

## Inventory

- **issue-select:** The preserved Unit 1 predecessor of issue-scout; its final saved run scored 19/20, with `issue-15` still a false accept. I keep it as the original evaluation baseline.
- **voice-guard:** Installed in Unit 6 with one canonical voice guide. Its first manual check caught my generic claim opening; I rewrote it to name #6458 and my action, then updated the comment after it passed.
- **Repository memory (`CLAUDE.local.md`):** Local Red-DiscordBot notes holding contribution rules, issue scope, and test commands; useful context, with no separate evaluation recorded.
- **ship-gate:** Installed in Unit 7 to run the documentation, style, and Python 3.8 manifest checks before live pr-precheck. The full gate passed after I corrected the inherited GitHub CLI path; its wiring canary was stopped as expected.
- **pr-monitor:** Installed in Unit 8 for post-ship review state. It supported waiting during bot-only activity, but missed an inline reviewer suggestion; on November 8 I updated the playbook to inspect inline comments before responding and retain a seven-day wait before considering one nudge.

## The Arc

**Beat 1**
Date: September 20, 2026
Friction: My issue selector treated related files and multiple named causes as evidence of an umbrella issue; the first two full runs scored 13/20 and 15/20.
Change: I revised bounded-scope to recognize work serving one goal and checked the remaining disagreements in a targeted run.
Result: The targeted run scored 5/5 and the final full run scored 19/20; I selected Path Review #62 for reproduction.

**Beat 2**
Date: September 28, 2026
Friction: A confirming reproduction-check evaluation rejected a human-authorship-policy case as if it required an AI-disclosure statement.
Change: I limited the disclosure check to explicit disclosure requests and reran the disagreement alongside canaries.
Result: The targeted run scored 5/5 and the final full run scored 20/20, including the disclosure category.

**Beat 3**
Date: October 4, 2026
Friction: The plan checker rejected a grounded inference and concrete mechanism because neither every causal detail nor the exact function had been established.
Change: I separated contradicted diagnoses from flagged inferences and allowed a concrete area plus mechanism to count as executable.
Result: The targeted run scored 10/10, including all wrong-cause and unbuildable canaries, and the final full run scored 20/20.

**Beat 4**
Date: October 18, 2026
Friction: My live PR review found that the upstream plan link resolved to literal `@comment.md`, even though the local course record contained the intended plan.
Change: I corrected the comment in place with a dated explanation, annotated the course record, and disclosed the original gap in the PR.
Result: The final live review accepted the package, PR #249 opened, and all five CI jobs succeeded at its recorded head.

**Beat 5**
Date: October 18, 2026
Friction: The migrated scout lacked a current-actionability check for reports whose requested fix might already exist.
Change: I added still-actionable and checked the current evidence for #288, #2, and #304.
Result: The targeted checks retained #288 and #2 and rejected #304 as already resolved; the later direct candidate grading supported choosing Red-DiscordBot #6458 and avoiding #6794's competing PRs.

**Beat 6**
Date: October 18, 2026
Friction: The ship gate could run its manifest checks but could not fetch the issue thread because its environment resolved legacy gh 1.12.1.
Change: I put the installed current GitHub CLI first on PATH and reran the full gate.
Result: Documentation, style, and Python 3.8 tests passed, and the live precheck accepted the contribution package.

**Beat 7**
Date: November 8, 2026
Friction: The PR monitor detected a review but missed the inline paragraph-spacing suggestion and drafted a request for information already present on GitHub.
Change: I discarded the draft, read the inline comment directly, added the requested paragraph break, and updated the playbook to check inline comments before responding.
Result: tox -e docs passed, I pushed commit 0c0e5d4f and replied to the reviewer. I have no later monitor run demonstrating that the playbook change prevents the same miss.

## Limitations and What's Next

My toolkit depends on the evidence it is given and on the environment its subprocesses inherit. An accepting verdict did not guarantee that my plan had actually been posted, and the monitor's review summary did not include the comment I needed to act on. The saved evaluations show useful behavior on those runs; they do not establish that the model will apply every rule consistently next time.

Next I would add an explicit check that posted comment bodies match the intended artifacts, record the CLI version used by each gate run, and test the monitor against a case with feedback present only in inline comments. I would also execute the documented PYTHON override for the Red contribution, which I inspected but did not run. The last recorded PR state is awaiting review; I have no evidence here of a merge or a successful later monitor run.

**Tool limits.**

issue-scout: The saved regression record still has a disagreement, and accepting a candidate does not settle maintainer wording choices or guarantee that the work matches my preferred bug/test profile.
repro-check: Its rubric can judge whether a supplied attempt targets the right trigger, but it cannot establish an unrecorded environment detail or make an unreproduced bug occur.
plan-check: Allowing a reasonable inference can admit a plausible wrong diagnosis when the supplied evidence does not expose the contradiction; the local plan also did not prove upstream publication.
pr-precheck: It accepted the package while flagging the broken plan comment as a caveat, so accept did not mean every finding was resolved; its evidence-based review also did not independently rerun my tests.
