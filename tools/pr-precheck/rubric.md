# Rubric: is this pull request ready to submit?

<!--
THIS IS THE PART YOU WRITE (fourth week running; this is the rubric's
final form in the sandbox). Your frame in SKILL.md executes whatever
checks you define here, via your procedure.md. It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the diff read against the plan's scope, the test
     evidence read against the plan's test plan, the description read
     against the diff, the repo-facts block's template asks) or a
     location from your references/evidence-guide.md. "The PR" is not
     a source; "the diff's changed files read against the plan's
     stated boundary" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself
     (does the diff fall inside the plan plus its deviation notes? is
     the claimed evidence observable?), never the write-up's shape
     (how long the description is, how many commits there are).
     Structure-shaped checks are what make graders disagree with
     themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (submit) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. State the `unclear`
   treatment explicitly: the frame here is YOUR SKILL.md, so a rubric
   that stays silent is only covered if your frame's grading
   discipline says what happens (the contract's own default is that
   an unverifiable claim fails).

Cover what actually gets bad PRs submitted. The failure families the
lecture named ARE the harness's scoring categories, same names as the
eval README: silent drift (the diff silently does more or less than
the posted plan, or the description claims fidelity the diff
contradicts), not tested (the evidence proves nothing observable, or
the repo's own checks were never run), unreviewable (debris or
unrelated hunks bury the change), and standards wall (the repo's
stated template and disclosure asks are ignored). Your evidence
guide's four headings map onto these one to one (plan fidelity =
silent drift, test evidence = not tested, diff quality =
unreviewable, standards and comms = standards wall), and the category
floor is scored on exactly these names plus clear accept. A rubric
that ignores a category will fail the eval packages built around
that category. And remember the honest-outcome
rule, fourth week running: a PR that honestly discloses a shortfall
can be ready; a rubric that equates "less than everything" with
"hold" fails the set.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| plan_fidelity | The `## Plan context` blocks a stated scope and files (eval), or the student's `plan.md` including ` ## Deviations` (live), read against the diff.| Every file changed in the diff falls inside the plan's stated file boundary, OR its inclusion is explained under a `## Deviations` note. A change not in the plan and not in Deviations fails.| required |
| description_matches_diff | The PR description's Summary and Changes sections, read against the diff. | Every change claimed in the description exits in the diff, and every substantial change in the diff is mentioned in the description. A description that claims "exactly as planned, no extra changes" over a diff with unannounced changes fails. | required. |
|  test_evidence_present | The `Test evidence` section (eval) or `test_evidence.md` (live). | Both a "before" (the reproduction of the bug) and an "after" (the fix verified) output excerpt are shown, with the commands that produced them. The repo's required checks (test-unit, lint, typecheck, etc.) have visible outcomes. "Tests pass" with no excerpt fails. | required |
| test_evidence_is_decisive | The test evidence, read against the plan's test plan. | The evidence re-runs the specific repro steps the plan or issue named — not a different path in the same file — and shows an observable outcome change. An evidence block that exercises an adjacent-but-unchanged path fails. | required |
| diff_is_clean | The diff from `git diff main...HEAD` (live) or the bundle's diff (eval). | No `print()` calls, `console.log`, `eprintln!`, `dbg!`, commented-out code blocks, `TODO`/`FIXME` on new lines, unused helpers behind `#[allow(dead_code)]`, formatting-only hunks, or unrelated edits beyond what the plan named. | required |
| template_sections_filled | The PR description, read against the repo's `.github/PULL_REQUEST_TEMPLATE.md` (live) or the `repo_facts` block's "pull requests" line (eval). | Every section the template names is filled with substantive content (or "N/A" with a reason). A missing section, an empty section, or a section filled with the template's placeholder text fails. | required |
| issue_reference_present | The PR description's Issue line. | TThe PR references the issue it implements by number — anywhere in the title, description, or commit messages. Accepted forms include `Closes #N`, `Fixes #N`, `Resolves #N`, `(#N)` in the title, or the issue number named in a Summary or Issue line. The reference must match the issue number in the URL or bundle's issue context. | required |
| disclosure_present | The PR description (Notes for Reviewers section). | When the repo has a stated AI-disclosure policy (repo_facts block names it), the description contains an AI-use disclosure consistent with that policy. When the repo has no stated policy, this check passes (the course still requires disclosure, but this check grades the repo's requirement). | required |
| maintainer_direction_engaged | The description (Notes section) and the thread highlights (eval) or the live issue thread. | If a maintainer's explicit direction appears in the thread (naming a specific approach, warning off an approach, requiring a config), the description engages with it — either follows the direction, or explains why a different choice was made. A description that ignores explicit maintainer guidance fails. | required |
| title_is_specific | The PR title (first line of `pr_draft.md` in live; `### Title` in eval). | The title names the change (what was fixed or added) and, ideally, the mechanism — not just "fix bug #N" or "address issue". | preferred |
| honest_disclosure_of_shortfall | The description, read against the plan and diff. | If the PR delivers less than the plan named (deferred item, unscoped part), the description says so plainly and names what was deferred. An honestly disclosed shortfall passes. If nothing was deferred, this check passes by default. | preferred |



## Verdict rule

<!-- State how the grades above combine into accept or reject, and how
unclear is treated. Example shape (write your own): "accept if every
required check passes; preferred checks never change the verdict;
unclear counts as fail." -->
Accept if **every required check passes**. A single required `fail` or `unclear` rejects the PR. Preferred checks never change the verdict; they rank accepted PRs (more preferred passes = higher rank). Unclear counts as fail for required checks, and as "not passed" for preferred checks.