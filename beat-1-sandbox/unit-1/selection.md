# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62

**Verdict output**

```
Live-mode grading of codepath/pathreview-ai301-fa26-s3#62 — "Health check references `settings.redis_host`, which does not exist on Settings"

- maintainer-alive: pass — Issue filed by Aburke225 (COLLABORATOR); repo pushed 2026-09-16, within 90 days of today (2026-09-20)
- repo-in-use: pass — Most recent default-branch commits are from 2026-09-16, well within 180 days
- bounded-scope: pass — Single identified root cause: probe reads settings.redis_host/redis_port instead of the existing settings.redis_url in api/routes/health.py
- not-blocked: pass — No mention of a dependency, pending decision, or maintainer clarification needed
- unclaimed: pass — No assignee, no linked PR; one classmate claim comment ("would like to work on this") is ignored per scope.md's Path Review house rule
- newcomer-guidance: pass (preferred) — Exact file and attribute names given: api/routes/health.py, settings.redis_host/redis_url
- verification-path: pass (preferred) — Repro given: GET /health with Redis running should not return 503 with 'redis': 'unhealthy'
- ai-policy-compatible: pass — No CONTRIBUTING.md, AI_POLICY.md, or AGENTS.md found; PR template requires test checkboxes but no AI-use ban or disclosure

```json
{
  "item": "codepath/pathreview-ai301-fa26-s3#62",
  "checks": [
    {"name": "maintainer-alive", "grade": "pass", "evidence": "Issue filed by Aburke225 (COLLABORATOR); repo pushed 2026-09-16, within 90 days of today (2026-09-20)"},
    {"name": "repo-in-use", "grade": "pass", "evidence": "Most recent default-branch commits are from 2026-09-16, well within 180 days"},
    {"name": "bounded-scope", "grade": "pass", "evidence": "Single identified root cause: probe reads settings.redis_host/redis_port instead of the existing settings.redis_url in api/routes/health.py"},
    {"name": "not-blocked", "grade": "pass", "evidence": "No mention of a dependency, pending decision, or maintainer clarification needed"},
    {"name": "unclaimed", "grade": "pass", "evidence": "No assignee, no linked PR; one classmate claim comment ('would like to work on this') is ignored per scope.md's Path Review house rule"},
    {"name": "newcomer-guidance", "grade": "pass", "evidence": "Exact file and attribute names given: api/routes/health.py, settings.redis_host/redis_url"},
    {"name": "verification-path", "grade": "pass", "evidence": "Repro given: GET /health with Redis running should not return 503 with 'redis': 'unhealthy'"},
    {"name": "ai-policy-compatible", "grade": "pass", "evidence": "No CONTRIBUTING.md, AI_POLICY.md, or AGENTS.md found; PR template requires test checkboxes but no AI-use ban or disclosure"}
  ],
  "verdict": "accept"
}
```
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

Four runs, in order:

1. First full run: **13/20** agreement.
2. Second full run (after revising scope checks and adding an AI-policy check): **15/20** agreement.
3. Targeted `--only` run on the five remaining disagreements (issue-01, issue-04, issue-13, issue-19, issue-20), used to iterate cheaply before spending a full run: **5/5** agreement.
4. Final full run, saved with `--save-run eval-run.txt`: **19/20** agreement, all category floors met. This is the run committed at `eval-run.txt` in this directory, whose agreement line reads:

   `agreement: 19/20 scored items  (bar: 18/20: PASS)`

   and whose category line reads:

   `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 3/4`

The last score above (19/20) matches the agreement line in the committed `eval-run.txt`.

**Issue analysis**

`issue-15` is the one remaining disagreement in the final saved run: the rubric graded **accept**, the gold label is **reject**. Gold's note for this item is "years of design debate and two abandoned PRs behind a friendly label." The bundle (zulip/zulip#19589) was opened in 2021, carries two closed, unmerged linked PRs, and includes a "Perhaps we can also..." line proposing an additional, undecided transformation on top of the base fix — matching the rubric's own age/prior-attempts language and its undecided-decision language. The rubric's `bounded-scope` check is written to fail issues open at least 2 years with at least 2 closed, unmerged implementation attempts, which this issue meets on paper. Despite that, the graded run returned `accept` on `issue-15` — the check's stated condition did not get applied consistently by the grading model on this run, and I did not spend further eval credit re-running `--only issue-15` alone to see whether a repeat run would have reproduced the correct reject, since the full run had already cleared the 18/20 bar and every category floor.

**Check rationale**

Quoted exactly from `rubric.md` as installed in `tools/issue-select/`:

> `bounded-scope` — "Pass if the issue requests one identifiable contribution serving a single goal, even if it spans several related files or lists several instances of the same underlying gap (for example, several call sites of one bug, or a doc page plus the existing pages that must point to it). A terse description, missing reproduction steps, or missing file names does not by itself fail this check. Fail if it is an umbrella/tracking issue whose sub-items are independent work that different contributors could claim and merge separately, a pure usage question, an unresolved design debate, or a change a maintainer says requires core-internal work. Also fail if a core implementation or product decision is left undecided (stated as TBD, unspecified, or "no alternatives identified") by the issue itself and no maintainer or collaborator has settled it, even absent visible thread debate. A maintainer's or collaborator's own diagnosis that names multiple root causes to fix, or lists several optional follow-on improvements alongside the required fix, is a settled scope, not an undecided one; it fails only if it asks the solver to pick which of several conflicting approaches to take. Also fail if the issue has been open at least 2 years and has at least 2 closed, unmerged implementation attempts. If the requested contribution cannot be identified, grade unclear."

I wrote it in this form because my first two full runs (13/20, then 15/20) both mis-rejected issues that were single-goal work spanning multiple files or multiple named causes — `issue-01`, `issue-04`, and `issue-19` — by treating any multi-part description as an umbrella. The added carve-out (a maintainer's own multi-cause diagnosis or several related files serving one goal is not an umbrella) was written specifically to stop punishing that shape of issue, while keeping the original umbrella/design-debate/core-internal-work fail conditions and the 2-year/2-abandoned-attempts fail condition intact for genuinely unbounded issues.

**Trade-offs**

The carve-out that fixed `issue-01`, `issue-04`, and `issue-19` (confirmed with a targeted `--only issue-01,issue-04,issue-13,issue-19,issue-20` run that returned 5/5 before I spent a full run) is what I believe let `issue-15` slip through as a false accept in the final run: loosening `bounded-scope` to tolerate a maintainer's multi-part diagnosis made the check less likely to flag `issue-15`'s "Perhaps we can also transform the mention..." line as an unresolved decision, even though the issue also independently meets the check's 2-year/2-abandoned-PR fail condition on its own terms. I accept this as a case the check can still miss on a given run, since the final 19/20 result clears the course bar and holds the category floor in every category, including `scope` (3/4).

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. **Fit to interests and time available.** #62 fits my interest in backend software and debugging. It gives me a concrete opportunity to trace how configuration is used by an API endpoint. For Unit 2, I wanted a localized issue where I could focus on setting up the environment, reproducing the bug, and verifying the fix.

2. **What the verdict got right, and what I weighed myself.** The verdict correctly identified a bounded problem with a named file and a concrete verification path. I preferred #62 over #56 because the configuration mismatch gave me a more direct starting point. Both passed the checks, but I felt more comfortable investigating the health endpoint than deciding how the structural chunker should handle headingless documents.

3. **Anticipated difficulty in claiming it.** I expect the main challenge to be getting the API and Redis running locally and distinguishing the configuration bug from a real connection failure. I will also need to check how the Redis client accepts a URL and verify that the endpoint still reports genuine Redis failures correctly. Since other students can work on the same issue, I will follow the course's shared-issue rules when claiming it and opening my PR.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
