# Evidence guide: where evidence lives in a PR package

<!--
THIS IS THE PART YOU WRITE (third week running: the map stays in your
hands). Your tool uses this guide as its map: for every kind of
evidence a rubric check names, this file says WHERE to find it in a PR
package and WHAT GOOD LOOKS LIKE when you do.

The four families below are the harness's failure categories under
the names the eval README uses: plan fidelity = silent-drift, test
evidence = not-tested, diff quality = unreviewable, standards and
comms = standards-wall. A package that fails none of them is a
clear-accept. Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the plan-context block's scope pair and test
  plan, the candidate PR's diff, commits, description, or
  test-evidence section, the repo-facts block's template asks and
  stated policy). In live mode (where in your working copy and on
  GitHub: your plan.md and its deviation notes, your branch's diff,
  your draft title and description, your captured test output, the
  repo's PR template and CONTRIBUTING.md).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("every changed file falls inside the
  plan's stated boundary or a deviation note") over adjectives ("the
  diff is clean").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts three ways: your
procedure says WHEN to gather each family, this guide says WHERE, and
your SKILL.md says the tool reads both. Write the map you wish your
executor had.
-->

## Plan fidelity (harness category: silent-drift)

<!-- Where the plan states its scope, boundary, and deviation notes,
and where the diff shows what actually changed. What it means for a
diff to match the plan, for an honest deviation to re-tie a mismatch,
and what silent drift looks like in each direction (more than the
plan, or less with no note). The description's fidelity claims read
against the diff live here too: a description claiming more or less
than the diff delivers is silent drift, not a comms problem. -->

**Where it lives:**
- Eval: the `## Plan context` block's stated Files-to-touch and scope boundary, plus any "Not in scope" line. The candidate PR's `### Diff` and `### Description` sections.
- Live: the student's `plan.md` (Files to touch, Scope, `## Deviations`), and the branch's diff from `git diff main...HEAD`.

**What good looks like:**
Every changed file in the diff is either in the plan's files-to-touch list or explained under `## Deviations`. Every "Not in scope" item in the plan is untouched in the diff. The description's claim of "exactly as planned, no extra changes" is true against the diff. Silent drift shows as: (a) files changed that the plan never named and no Deviations note explains, or (b) files named in the plan that the diff does not touch and no Deviations note explains, or (c) a description claiming something the diff shows differently.


## Test evidence (harness category: not-tested)

<!-- Where the PR shows its proof: the test-evidence section's
before/after against the plan's test plan and the reproduction's own
steps, and the outcome of the repo's own checks or suite. What
decisive looks like (an observable behavior named, the expected-after
stated, the checks' outcome visible) next to "tests pass". -->

**Where it lives:**
- Eval: the candidate PR's `### Test evidence` block, read against the plan's Test plan.
- Live: the student's `test_evidence.md`, read against the plan's Test plan and the repo's required checks.

**What good looks like:**
Both a "before" (the bug reproducing) and an "after" (the fix verified) appear, each with the command that produced it and its output. The commands re-run the plan's named repro steps — not an adjacent path in the same file. The repo's required checks (test-unit, lint, typecheck, integration) appear with their actual output (pass or fail). "Tests pass" without an excerpt is not decisive. "Verified locally, works" without a repro before/after is not decisive. A failing check reported honestly with the reason is decisive.


## Diff quality (harness category: unreviewable)

<!-- Where the change itself lives: the unified diff and the commit
list. What a reviewable change looks like (the fix visible, nothing
unrelated riding along) and the debris tells: debug leftovers, dead
code, commented-out blocks, formatting churn, drive-by edits. -->

**Where it lives:**
- Eval: the candidate PR's `### Diff` and `### Commits`.
- Live: `git diff main...HEAD` and `git log main..HEAD`.

**What good looks like:**
The diff shows only the change the plan named. No debug leftovers: `print(`, `console.log`, `eprintln!`, `dbg!`, `System.out.println` on new lines. No commented-out blocks that aren't clearly explanatory comments (a `// was: x` block is debris; a `// see #1234` comment is fine). No unused functions behind `#[allow(dead_code)]` or equivalent. No formatting-only hunks (whitespace or import reordering with no behavior change). No unrelated files. Commit messages that describe the change ("fix: wrap DB probe in text()") pass; "wip", "fix", "misc cleanups", "fmt" fail.



## Standards and comms (harness category: standards-wall)

<!-- Where the repo states its asks (the repo-facts block's PR
template sections, contributing instructions, and stated policy,
including AI-use disclosure) and where the PR honors them: the
stated sections filled with real content, the disclosure present,
explicit maintainer direction in the thread engaged. What compliant
looks like next to boilerplate or a visibly ignored ask. (Whether
the description's claims match the diff is plan fidelity, above.) -->

**Where it lives:**
- Eval: the `## Repo facts` block's "pull requests" line (what the template requires) and "contribution policy" line (AI-disclosure asks). The candidate PR's `### Description`.
- Live: `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, and any `AI_POLICY.md`/`AGENTS.md`, plus the student's `pr_draft.md`.

**What good looks like:**
Every template section the repo names appears in the description with substantive content. The AI-disclosure policy (if stated) is honored in Notes for Reviewers. Any explicit maintainer direction in the issue thread is engaged — followed or explained. Standards-wall failures show as: a missing template section, a section filled with the template's own example text, no AI disclosure when the policy requires one, or a description that ignores a maintainer's explicit guidance.

