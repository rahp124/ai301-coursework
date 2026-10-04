# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

- **Where it lives:** In an eval bundle, the candidate plan's `Diagnosis` section (or
  whatever its first section states as the cause), read against every numbered step,
  control run, and artifact in the `## Repro evidence` block. In live mode: the student's
  `plan.md` Diagnosis section, read against their own posted repro comment on the issue
  (or, on a house issue, the house repro pack quoted in the drafts).
- **What good looks like:** Walk the repro evidence step by step and ask, for each step,
  "does the stated cause predict this exact result?" A control run (same inputs minus one
  variable, a debug trace, a second environment) that isolates a *different* cause is the
  single strongest signal — if the stated cause cannot explain why the control behaved the
  way it did, the diagnosis is wrong, not just unproven. A diagnosis the evidence is silent
  on (never ruled in or out by any step) is `unclear`. A diagnosis every step is consistent
  with, including any control, is grounded.

## Scope

- **Where it lives:** The plan's explicit in-scope / not-in-scope statement, plus its
  Files and Approach sections read together — scope creep often shows up as *extra* files
  or approach steps that go beyond what the in-scope line claims, not as a missing scope
  line.
- **What good looks like:** One bounded change that maps onto the diagnosed cause: a
  specific function, site, or narrow set of files. Deferring a related, larger concern to
  a named follow-up ("the tombstone rework is a separate issue, deferred") is a pass and
  often a point in the plan's favor. A fail looks like a one-constant fix that grows into
  a rewrite of the surrounding module, a migration, a new abstraction layer, a settings
  panel, or several unrelated improvements bundled into "while I'm in here."

## Executability

- **Where it lives:** The plan's Files and Approach sections.
- **What good looks like:** Named file(s) or area(s) in the actual repo structure, and an
  ordered list of concrete steps ("extend regex X," "add a generation counter to the page
  struct," "build the Redis client from `settings.redis_url` instead of the missing
  `redis_host`/`redis_port` fields"). A fail names no file, defers the choice ("gocui?
  tcell? not sure"), or describes an outcome instead of an action ("make it robust,"
  "investigate the stack," "fix it, wherever that ends up living").

## Test plan

- **Where it lives:** The plan's Test plan section, read against the repro evidence's
  steps and artifacts (the eval bundle's repro-evidence block; live mode, the student's
  posted repro comment).
- **What good looks like:** A decisive test plan re-runs (or names a new test deriving
  from) the same trigger the repro evidence used, and states what the observable
  pass/fail result will be (an exit code, a field value, a specific log line's absence).
  A fail names no observable result ("should feel faster," "seems fixed," no test plan at
  all) or tests something other than the diagnosed behavior.

## Honesty

- **Where it lives:** Anywhere the plan states (or fails to state) risk, uncertainty, or
  an open question — commonly a Risks/unknowns section, but also tone throughout the
  Diagnosis and Approach sections.
- **What good looks like:** At least one specific, real open question the plan has not
  yet verified, or an explicit, specific statement that there are none and why (not just
  silence). False confidence looks like treating every claim as settled, including facts
  the repro evidence never actually pinned down — that case is first a `diagnosis-grounded`
  problem if the false confidence is about the cause, and a `honest-unknowns` fail
  otherwise (confidence about scope, side effects, or risk the plan never checked).

## Comms

- **Where it lives:** The plan comment's text, read against the issue's `## Thread
  highlights` (eval bundle) or the live thread (live mode), and against the repo-facts
  block's stated AI-use policy (eval bundle) or the repo's actual `CONTRIBUTING.md` / AI
  policy docs (live mode).
- **What good looks like:** When a maintainer has proposed a direction, rejected an
  approach, or raised an open question in the thread, the plan comment visibly follows or
  engages it (agrees and builds on it, or explicitly explains a deviation) rather than
  silently proposing something else as if the thread said nothing. When the repo's policy
  explicitly asks commenters to disclose AI use and its extent, the posted comment states
  what tool was used and how much — a policy that only asks for human-reviewed or
  human-written comments does not trigger this; only an explicit disclosure ask does.
