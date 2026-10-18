# Procedure: how this tool grades a PR package

## Read order

1. Determine live or eval mode using SKILL.md. Complete its live scope and voice reads when applicable. Confirm the four grading components contain real content before grading.
2. Read the issue and thread, then repo-facts requirements (live: target thread, template, CONTRIBUTING and CI). Record the target behavior, maintainer directions, mandatory checks and communication/disclosure requirements. Fix these before reading the candidate's claims.
3. Read plan context, original repro, scope pair, approach, files, test plan and deviation notes. List expected deliverables, excluded behavior, observable success criteria and justified changes.
4. Read candidate diff and commits in full, then title/description and test evidence. List actual behavior changes and claimed results independently so the description cannot hide a missing hunk.

## Evidence gathering

1. Plan fidelity: pair each promised deliverable and excluded behavior with diff hunks. Record an unexplained missing deliverable or added behavior, or the precise deviation that accounts for it. Compare the description's fidelity claims to the diff.
2. Test evidence: pair each claimed result with a command/step and observed artifact, then with the plan's original trigger. Separate tests merely added from tests actually run. Inventory each required repo check as run/passed, run/failed, blocked, explicitly omitted with reason, or absent; distinguish known unrelated failures from introduced failures. Record the actual limitation, not a rounded-up success.
3. Diff quality: walk all files and hunks and classify their connection to the bounded fix. Record debris/unrelated churn and whether it obscures review, independently of whether the draft mentions it.
4. Standards and comms: compare each applicable template/policy ask to a specific part of the outgoing text or diff. Record required disclosure wording and the actual disclosure, naming/commit conventions, issue linkage and any issue-specific suppression/xfail requirement. Compare reviewer orientation with the actual change.

## Check execution

Grade every check once in rubric table order using those notes and the evidence guide. Use pass or fail where the package settles the condition; use unclear when needed evidence is missing. Quote the deciding fact, including explicit absence when that decides the grade. A missing optional policy is not a fabricated requirement: distinguish 'no policy stated' from no evidence about policy. Revisit a source only to resolve a specific contradiction or missing note, not to invent a new check. Report procedure gaps rather than adding runtime rules. In live mode report voice violations separately, quoting the broken rule.

## Verdict assembly

Apply the rubric's verdict rule exactly. Required unclear counts as fail; preferred never gates. In the readable summary quote the first nonpassing required check in table order as the deciding reason, and list all other findings. If all required checks pass, say which evidence supports readiness and keep honest limitations visible. Emit all checks with name, grade and one-line evidence, then the binary verdict, using the fixed final JSON schema from SKILL.md. Nothing follows it. Never modify or post the reviewed artifacts as part of grading.
