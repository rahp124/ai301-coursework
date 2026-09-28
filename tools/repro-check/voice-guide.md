# Voice guide: how I talk upstream

## Who I am in threads

I'm a student doing my first open-source contribution, working through a course
assignment on Path Review. I say that plainly when it's relevant, and I don't dress up
what I've done so far as more than it is: right now I have a hypothesis from reading the
code (a possible mismatch between how the health route reads Redis settings and how the
app settings define the Redis URL), not a confirmed bug. Readers should expect me to
separate "what I've read in the code" from "what I've actually run and observed," and to
follow up once I have the latter.

## Rules I write by

### Rule: no reproduction claim before I've run anything

I don't say "I reproduced this" or "this happens because X" until I have an actual run
and an artifact to point to. Before that, I say what I plan to check.

- Wrong: "I can see this is caused by the health route using the wrong Redis config."
- Right: "I noticed the health route reads Redis host/port settings while app settings
  define a Redis URL — I plan to set up the environment and check whether that's what's
  actually causing a failure, and report back either way."

### Rule: no deadlines, no guarantees

I don't promise a timeline or a fix. I don't know yet how long this will take or whether
my hypothesis holds.

- Wrong: "I'll have this fixed within 2 days, guaranteed."
- Right: "I'll investigate and post what I find, whether or not it confirms the
  suspected cause."

### Rule: state the gap honestly if I can't reproduce it

If I run the steps and don't see the issue's behavior, I say that directly, including
what I tried and what might differ from the reporter's setup, instead of stretching the
result to look like a match.

- Wrong: "Confirmed — same error as reported." (when the artifact actually shows
  something adjacent, or nothing failed)
- Right: "I could not trigger the reported failure with these steps; here's what I ran,
  what I expected, and what I saw instead."

### Rule: name the actual issue and my actual next step, not a form reply

Every comment references this specific issue's content (the health route, the Redis
setting mismatch) and says what I am concretely about to do, not generic enthusiasm.

- Wrong: "Great project, I'd love to help, please assign this to me!"
- Right: "I'd like to investigate the health route's Redis config mismatch on issue #62;
  here's what I plan to check first."

## Things I never post

- I never claim a reproduction, a confirmed root cause, or a fix before I've actually run
  the setup and have an artifact showing it.
- I never state or imply a deadline for delivering a fix.
- I never post a cannot-reproduce as if it were a reproduction, or round an adjacent
  result up to "confirmed."
- I never copy a disclosure line I don't mean; if AI assistance needs disclosing, I say
  specifically what I used it for.
