# Procedure: how this skill grades a plan package

## Read order

1. Read the issue context first (title, body, repo-facts block) to fix what behavior is
   actually being reported and what the repo's stated conventions and AI policy are.
   Note: the stated bug, and the repo's disclosure policy wording verbatim if present.
2. Read `## Thread highlights` next, before the plan. Note any explicit maintainer-
   proposed direction, rejected approach, or open question — this is the fact the
   `comms-thread-aware` check needs, and reading it before the plan prevents the
   diagnosis from anchoring the read of the thread.
3. Read `## Repro evidence` next, in full, step by step. Note what behavior each step
   establishes, and flag any control run or debug trace that rules a cause in or out.
   This is read before the plan so the plan's diagnosis is judged against the evidence,
   not the other way around.
4. Read the candidate plan last (Diagnosis, Scope, Files, Approach, Test plan, Risks),
   then the candidate plan comment. Note the stated cause, the in/out-of-scope lines, the
   named files and approach steps, the test plan's stated observable outcome, and any
   stated risk or unknown.

## Evidence gathering

- **diagnosis-grounded**: walk the repro evidence's steps in order; for each, check
  whether the plan's stated cause predicts that step's result. Record any step (control
  run, debug trace, a second environment or input) that the stated cause cannot explain —
  that single step is the deciding evidence, quote it.
- **scope-bounded**: compare the plan's named Files/Approach against its own in/out-of-
  scope lines. Record the count and nature of named files/steps: one narrow site vs. a
  spread across unrelated modules, new abstractions, or migrations.
- **executable**: check the Files and Approach sections name actual repo paths/areas and
  ordered concrete actions. Record the most concrete (or most vague) line verbatim.
- **test-plan-decisive**: check the Test plan section maps onto the same trigger as the
  repro evidence and states an observable result. Record the stated expected result
  verbatim, or its absence.
- **honest-unknowns**: scan the plan for a stated risk/unknown or an explicit "no open
  questions, because X" statement. Record the quoted risk, or its absence.
- **comms-thread-aware**: compare the plan comment against the thread-highlights note
  from step 2. If a maintainer direction exists, record whether the comment follows,
  engages, or ignores it.
- **disclosure-when-required**: compare the repo-facts AI-policy line against the plan
  comment text. Record whether the policy explicitly asks for disclosure, and if so,
  whether the comment contains one.

## Check execution

Grade checks in the table order above (diagnosis-grounded, scope-bounded, executable,
test-plan-decisive, honest-unknowns, comms-thread-aware, disclosure-when-required). Each
check is graded once, from the notes gathered in the Evidence gathering stage above — do
not re-read the whole package per check. Grade `pass` or `fail` when the gathered note
settles it; grade `unclear` only when the package genuinely contains no evidence either
way (not when the evidence is merely terse). For `comms-thread-aware` and
`disclosure-when-required`, "no explicit maintainer direction" and "no explicit
disclosure ask" are each a definitional pass, not an `unclear` — the check simply does not
fire.

## Verdict assembly

Apply the rubric's verdict rule: `accept` only if every `required` check
(diagnosis-grounded, scope-bounded, executable, test-plan-decisive, comms-thread-aware,
disclosure-when-required) is graded `pass`. Any required check graded `fail` or
`unclear` produces `reject`. `honest-unknowns` is `preferred` and is reported in the
output but never changes the verdict. In the output JSON, the `evidence` field for the
deciding check(s) must quote the specific repro-evidence step, thread line, or plan
sentence that produced the grade — not a paraphrase.
