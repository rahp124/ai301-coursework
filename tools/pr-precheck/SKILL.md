---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading



## The question

Grade exactly one PR package to decide: is this ready to submit? Read its title, description, commits, diff, and test evidence against its issue and the accepted plan, including recorded deviations. Execute this tool's components rather than inventing checks.

## Inputs and modes

Choose one mode explicitly.

- Live mode: read the student's `plan.md` including deviations, `git diff main...HEAD` and its stat from the working copy, branch commits, `pr_draft.md` (title first, then description), and `test_evidence.md`. Verify that local main matches the target base; report a stale base rather than silently switching comparisons. Gather the issue body and complete thread, actual PR template, contribution requirements, and stated AI policy from the target repository using gh/API or repository files. A house-chain student supplies the house plan and house repro pack instead of their own plan and posted reproduction. Identify the package by its PR URL, or the target issue URL for an unopened draft.
- Eval mode: the supplied bundle is the whole world. Use its issue context, thread, repo facts, plan context, candidate PR and test evidence only. Fetch nothing and read no other files. Grade every check with the full verdict rule; use the bundle id as item.

## The scope seam (live mode only)

Read `scope.md` in this installed directory before any other live input. Apply its repository boundary and house rules. If Repo is an unfilled placeholder, stop without grading and tell the student to fill the Repo line with their section's Path Review repo. Refuse to grade a different target repository. Check the upstream target, not merely the fork's origin. Eval mode ignores scope.md entirely.

## The voice seam (live mode only)

Read `voice-guide.md` in live mode and compare the outgoing PR title and description to its rules. Quote any broken rule in the readable summary. Voice alone never changes the verdict unless a rubric check explicitly reads it. Eval mode ignores voice-guide.md entirely.

## Component reads

Read `rubric.md` for checks, weights and verdict rule, `references/evidence-guide.md` for evidence locations, and execute `procedure.md` as written. Refuse to grade when the rubric has no actual checks or the procedure has no actual steps; template comments do not count. Report any procedure gap instead of silently improvising around it.

## Verdict and output

Use only `accept` (ready to submit) or `reject` (hold). A readable check summary and live voice notes may precede the JSON. End with the following fenced JSON block, using real values, the same field order, and no extra fields. Nothing follows the block.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

Name the deciding fact or quote for every grade. Grade substance against the plan, evidence and stated standards, never polish, length, package identifiers or expected labels. Apply each written pass condition even if intuition disagrees; report the tension for a later rubric revision. Follow the procedure and report its gaps. Distinguish explicit absence of a policy (a conditional check can pass) from missing policy evidence (unclear). Apply the verdict rule to unclear; if it is ever silent, unclear counts as fail. An honest evidenced limitation can be ready; concealment or unsupported success claims cannot.
