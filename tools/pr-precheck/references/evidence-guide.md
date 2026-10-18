# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

Eval: read plan-context's repro, scope pair, files, approach and deviations against the candidate PR's unified diff, commits and description. Live: read plan.md (including Deviations), the student's posted plan/repro or supplied house pack, git diff main...HEAD and its stat, git log main..HEAD, and pr_draft.md. Verify main against the upstream base before using it.

Good evidence accounts for every promised behavior and every changed behavior. A named omission or changed approach with a concrete reason in the plan is honest deviation; an extra feature or missing promised branch silently contradicted by 'exactly as planned' is drift. File count alone cannot establish scope.

## Test evidence (harness category: not-tested)

Eval: candidate test-evidence section and test diff, description's test claims, plan-context test plan and original repro, and repo-facts checks. Live: test_evidence.md and captured outputs, test source, Unit 2 repro comment, plan.md test plan, CONTRIBUTING and CI workflow commands. Check the environment/revision when present and distinguish historical evidence from new runs.

Good evidence pairs an actual command or reproducible step with observable output tied to the original trigger: a field value, exit status, before/after failure signature or regression-test result. Added tests without a run and 'seems fixed' prove nothing. A partial result or cannot-reproduce can be adequate if observable, honestly bounded and not inflated into a fix claim. Required check omissions need explicit reasons; a disclosed blocker is different from silence. No-tests-collected is not 'integration tests passed'; record the exit code and any repository CI exception.

## Diff quality (harness category: unreviewable)

Eval: all unified diff hunks, changed paths and commit list. Live: git diff main...HEAD, git diff --stat main...HEAD, git log main..HEAD and status for accidental staging. Compare with the plan and deviations, but judge whether debris obscures review independently.

Good evidence shows the smallest coherent behavior change with relevant regression coverage and required bookkeeping. Unrelated bulk formatting, generated output, debug dumps and accidental scratch files can bury even a correct fix. Large justified changes can pass; disclosure of junk alone does not make it reviewable.

## Standards and comms (harness category: standards-wall)

Eval: repo-facts template asks, contribution requirements and AI policy, thread highlights, candidate title/description/commits and relevant diff hunks. Live: target repository's actual PR template, docs/CONTRIBUTING.md, relevant CI/config, full issue thread, branch/commit metadata and pr_draft.md. Read voice-guide.md only live and report its rules separately.

Good evidence satisfies each stated applicable requirement with actual content. Include issue linkage and substantive requested sections rather than empty headings; disclose AI use and extent when explicitly required. Evaluate actual policy wording rather than inventing universal disclosure or template rules. Explicitly absent policy creates no new obligation; unavailable policy evidence remains unclear. A pending CI checkbox must stay unticked until CI is observed green. Explain backend-only screenshot omissions and distinguish unrelated seeded suppressions from those fixed by this PR.
