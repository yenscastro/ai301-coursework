# Evidence guide: where proof lives in a reproduction package

<!--
THIS IS THE PART YOU WRITE (new this week: Unit 1 handed you this file
finished; the scaffolding fades). The skill uses this guide as its map:
for every kind of proof a rubric check names, this file says WHERE to
find it in a package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repo-facts block, the
  claim comment, the repro report and its parts). In live mode (where
  on GitHub or in the draft: the issue thread, the repo's docs, the
  student's draft comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the versions named match what the
  issue targets, or the difference is called out") over adjectives
  ("environment is thorough").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute; the rubric swap showed you what that feels
like. Write the map you wish your grader had.
-->

## Environment

<!-- Where the environment record lives, and what a sufficient one
looks like against the issue's stated target. -->

**Where it lives:**
- **Eval bundle:** the repro report's environment section (typically a
  block headed "Environment" or "Setup" near the top of the report).
  Also cross-check the `repo:` line and the "latest release" / "last
  push" lines in the repo-facts block against what the report claims.
- **Live mode:** the draft repro comment's environment block, and the
  issue thread's stated target (the version, OS, or platform the issue
  was filed against, if the issue names one).

**What good looks like:**
The report names (a) the operating system and version, (b) the
runtime/language version(s) that matter to the bug, and (c) the repo
revision — a commit SHA, a branch name, or a release tag. If the
issue states a target platform and the report runs on a different one,
the report calls that difference out explicitly ("issue was filed on
Linux; reproduced on macOS 14.5 with the same error"). Missing any of
the three required pieces is a fail, not a partial.

## Steps

<!-- Where the reproduction steps live, and what makes them followable
by a stranger, starting state to trigger. -->

**Where it lives:**
- **Eval bundle:** the repro report's steps section (numbered or
  bulleted instructions between the environment block and the output
  excerpt).
- **Live mode:** the same section of the student's draft repro comment.

**What good looks like:**
Every step names a concrete action: a command to run, a file to open,
a line to change, a button to click. The starting state is stated
(dependencies installed, database seeded, env vars set, branch checked
out). The steps proceed from that starting state to the trigger that
provokes the bug. A stranger reading the report can follow it
end-to-end without asking a clarifying question. Steps that assume
unstated context ("install the usual dependencies", "run the tests")
do not pass.

## Behavior shown

<!-- Where the artifacts live (output excerpts, logs, screenshots),
and what it means for an artifact to show the issue's behavior rather
than an adjacent one. -->

**Where it lives:**
- **Eval bundle:** the repro report's output excerpt — a code block,
  a log excerpt, or a screenshot description showing the failure. Also
  the issue context's "What happened?" section and any error text the
  issue quotes.
- **Live mode:** the same section of the draft repro comment, read
  against the live issue's described behavior.

**What good looks like:**
The report's output shows the same behavior the issue describes. Match
on the *specific failure*: the same error class (`KeyError`, not
`AttributeError`), the same failing assertion or test name, the same
wrong value or the same crash location. A related failure in the same
area does not pass — if the issue says "crash on X", the excerpt must
show a crash on X, not a warning on X or a crash on Y. If the issue
quotes an exact error string, the excerpt should contain that string
(or an explanation of why the string differs while the behavior
matches).

## Honesty

<!-- Where claims and their backing meet: how to tell a report that
says exactly what happened (including an honest cannot-reproduce) from
one that claims more than its evidence shows. -->

**Where it lives:**
- **Eval bundle:** the repro report's outcome statement — the sentence
  or two where the author says whether they reproduced, partially
  reproduced, or could not reproduce the behavior. Read this against
  the output excerpt.
- **Live mode:** the same section of the draft repro comment.

**What good looks like:**
The report states plainly what happened. An *evidenced cannot-reproduce*
is a pass: if the author ran the steps and saw something other than the
described bug, saying so — with the output that shows what they did see
— is the honest answer. A report that claims reproduction but whose
excerpt shows different behavior fails. A report that hedges without
committing ("seems like it might reproduce") fails unless it also
commits to what was observed. The outcome must match the evidence in
the same report.

## Comms

<!-- Where the words meet the repo: the claim comment against the
issue, the comments against the repo's stated templates and
contribution policy (including AI-use disclosure requirements), and
what specific-and-honest looks like next to boilerplate. -->

**Where it lives:**
- **Eval bundle:** the repo-facts block's "contribution policy" line
  (this is where AI-use policies and disclosure requirements appear),
  the claim comment, and the repro comment's opening and closing
  sentences.
- **Live mode:** the repo's `CONTRIBUTING.md`, `AI_POLICY.md`,
  `AI_USAGE_POLICY.md`, or `AGENTS.md` (linked from the repo's root or
  `.github/`); the issue thread's house rules; the student's draft
  comments.

**What good looks like:**
- **Policy compliance:** if the repo's stated policy bans AI-generated
  contributions, the package fails outright. If the policy requires
  disclosure of AI assistance, the comments disclose, in the author's
  own words. Silence on AI in the policy passes; disclosure is not
  required when the repo hasn't asked for it.
- **Issue specificity:** the claim comment names the specific issue
  (title, number, or a one-line restatement of the bug) and what the
  author will do next. Boilerplate ("I'd like to work on this") does
  not pass.
- **Promise vs. assertion:** on a claim-only draft, the comment
  promises the report to come; it does not assert reproduction results
  it doesn't yet have, and it does not promise a fix or a date. The
  repro comment is where results are asserted, backed by the report's
  evidence.
- **Signal of specificity:** the repro report quotes at least one
  artifact verbatim — a real error line, a real test failure, real
  command output — rather than paraphrasing. Paraphrase-only reports
  are weaker than ones with a quoted excerpt.