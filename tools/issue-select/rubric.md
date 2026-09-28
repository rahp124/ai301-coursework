# Rubric: is this a good first issue?

<!--

THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks

you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:

   - Check: a short name (used in the output JSON).

   - Evidence: exactly what to look at, and where. Name the source

     (repo-facts block, issue body, comment thread, or the locations in

     references/evidence-guide.md). "The repo" is not a source; "the last

     5 default-branch commit dates" is.

   - Pass condition: a condition someone else could apply and get your

     answer. Prefer thresholds with numbers ("a maintainer commented

     within 30 days") over adjectives ("maintainer is responsive").

   - Weight: `required` (a fail here rejects the issue) or `preferred`

     (never changes the verdict; a nice-to-have that helps rank the

     issues you accept).

2. A verdict rule below the table: how the check grades combine into

   accept or reject, including how `unclear` is treated. The verdict

   space is binary. If you write no rule for `unclear`, the skill treats

   it as fail.

Cover what actually kills first contributions. The lecture named four

families: the maintainer is alive, the repo is in use, the scope fits a

newcomer, and nobody else is already on it. A rubric that ignores a family

will fail eval issues designed around that family.

-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| maintainer-alive | The issue body and comment thread, plus the dates of the last 5 default-branch commits in the repo-facts block. | Pass if either a maintainer commented on the issue within the last 90 days or at least 1 of the last 5 default-branch commits occurred within the last 90 days. | required |
| repo-in-use | The dates of the last 5 default-branch commits in the repo-facts block. | Pass if at least 2 of the last 5 default-branch commits occurred within the last 180 days. | required |
| bounded-scope | The issue body and Comments section, including maintainer statements, plus linked PR history under Repo facts. | Pass if the issue requests one identifiable contribution serving a single goal, even if it spans several related files or lists several instances of the same underlying gap (for example, several call sites of one bug, or a doc page plus the existing pages that must point to it). A terse description, missing reproduction steps, or missing file names does not by itself fail this check. Fail if it is an umbrella/tracking issue whose sub-items are independent work that different contributors could claim and merge separately, a pure usage question, an unresolved design debate, or a change a maintainer says requires core-internal work. Also fail if a core implementation or product decision is left undecided (stated as TBD, unspecified, or "no alternatives identified") by the issue itself and no maintainer or collaborator has settled it, even absent visible thread debate. A maintainer's or collaborator's own diagnosis that names multiple root causes to fix, or lists several optional follow-on improvements alongside the required fix, is a settled scope, not an undecided one; it fails only if it asks the solver to pick which of several conflicting approaches to take. Also fail if the issue has been open at least 2 years and has at least 2 closed, unmerged implementation attempts. If the requested contribution cannot be identified, grade unclear. | required |
| not-blocked | The issue body and comment thread. | Pass if neither source says work must wait for another unresolved issue, an unreleased dependency, a pending design decision, or maintainer clarification before implementation can begin. A maintainer's own diagnosis of multiple causes or a list of optional follow-on suggestions is not a pending design decision merely for containing more than one item; it blocks only if the issue or thread says the direction still needs to be chosen or confirmed by someone else. | required |
| unclaimed | The issue assignee information in the repo-facts block, the issue's linked-PR history in the repo-facts block, and the issue comment thread. | Pass if the issue has no assignee, no open linked PR against it, and no commenter has said they are currently working on it or claimed it within the last 30 days without later withdrawing the claim. In live mode, apply scope.md's Path Review claim-comment exception where it says to; that exception never covers assignees or open linked PRs. | required |
| newcomer-guidance | The issue body and comment thread. | Pass if at least 1 relevant file, directory, function, command, test, documentation page, error message, or implementation starting point is identified. | preferred |
| verification-path | The issue body and comment thread. | Pass if they identify at least 1 objective way to verify completion, such as a named test, command, expected output, before-and-after behavior, or documentation requirement. | preferred |
| ai-policy-compatible | The contribution policy line under Repo facts; in live mode, CONTRIBUTING.md in the root or .github/, linked contributor policies, dedicated AI policy files, and PR templates. | Fail if the policy explicitly bans AI-generated or AI-assisted contributions needed for this course workflow. Pass if the policy is silent or permits AI use subject to disclosure, personal understanding, testing, or human review. If policy evidence cannot be accessed or its meaning cannot be determined, grade unclear. | required |

## Verdict rule

Measure eval recency thresholds against the bundle's capture date, not today's date. In live mode, measure against today and apply scope.md's candidate restrictions and Path Review claim exceptions. Missing evidence is different from an explicitly silent policy or an explicitly empty assignee/PR list.

Accept if every required check passes. Reject if any required check fails or is unclear. Missing evidence needed to decide a required check counts as unclear; do not invent supporting facts.

Preferred checks never change the verdict. Rank accepted issues by the number of passing preferred checks, highest first. Treat unclear preferred checks as not passing for ranking. Break remaining ties in favor of the issue with the smaller bounded scope.