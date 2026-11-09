# Procedure: how this tool grades a PR package

<!--
THIS IS THE PART YOU WRITE (second week running for the procedure).
Week 3 you wrote these steps for a plan package; this week the graded
object is a PR package, and the read that matters most is a
side-by-side: the diff against the plan, the evidence against the test
plan, the description against both. Your week-3 procedure is the
pattern; do not paste it unchanged, because its read order was built
for a different object.

Your rotation is the design brief again, and this week friction routes
three ways: a stall on WHAT to decide is a rubric gap, a stall on
WHERE to look is a procedure gap (this file), and a stall on what the
tool even reads or outputs is a frame gap (your SKILL.md). A complete
procedure lets someone who has never seen a PR package before grade
one exactly the way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
plan's scope pair before opening the diff, and list the files the plan
names" is a step; "understand the change" is a wish.
-->

## Read order

<!-- What gets read, in what order, before any check is graded, and
what to note down from each part while reading. A complete procedure
decides where the plan sits in the order (before the diff? before the
description?) and says why the order matters for the side-by-side
checks that come later. -->
1. **Issue context first** (live: the issue URL; eval: the `## Issue` and `## Thread highlights` sections). Note the described behavior, the expected outcome, and any maintainer direction in the thread.
2. **Repo facts next** (eval: `## Repo facts`; live: `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`). Note the template sections required, the contributing asks, and any AI-disclosure policy.
3. **Plan context** (eval: `## Plan context`; live: the student's `plan.md`). Note the stated files, scope boundary, test plan, and any `## Deviations` notes.
4. **The diff** (eval: `### Diff`; live: `git diff main...HEAD` from the working copy). List every file changed and the shape of each hunk.
5. **The PR description** (eval: `### Description`; live: `pr_draft.md` after the first line). Read the title, Summary, Issue, Changes, Testing, Screenshots, Notes.
6. **Test evidence** (eval: `### Test evidence`; live: `test_evidence.md`). Note the before/after commands and outputs, and the repo checks' outcomes.
7. Only after all six are read, begin grading.

**Why this order:** the plan must be read before the diff so plan-fidelity is a comparison, not a memory test. The description must be read after the diff so description-claims-vs-diff is a comparison, not a trust exercise.


## Evidence gathering

<!-- For each evidence family your rubric's checks name, the concrete
gathering move: which part of the package (or, live, which file, diff,
or page per your evidence guide) to pull the fact from, and what to
record. The load-bearing gathers this week are pairings: diff files
against plan scope, claimed evidence against the plan's test plan and
the repo's checks, description claims against diff contents. A
complete procedure leaves no check whose evidence an executor would
have to hunt for. -->

For each check in the rubric:

- **plan_fidelity:** gather (a) the plan's Files-to-touch list, (b) the plan's scope boundary, (c) the diff's file list, (d) any `## Deviations` notes. Compare each diff file against (a) and (b); if not present, check (d).
- **description_matches_diff:** gather the description's Changes bullets and Summary claims, and the diff's files and hunks. Every diff file must appear in the description; every described change must appear in the diff.
- **test_evidence_present:** look for a "Before" heading or block and an "After" heading or block in the evidence. Both must be present and show command + output. Then look for the repo's named checks (test-unit, lint, typecheck) with visible outcomes.
- **test_evidence_is_decisive:** read the plan's Test plan. Identify the specific repro steps named. Check that the evidence block re-runs those steps (or their exact equivalent) — not an adjacent path.
- **diff_is_clean:** scan the diff for tell-signs: `print(`, `console.log`, `eprintln!`, `dbg!`, `TODO`, `FIXME`, commented-out blocks (`// import ...` where the import isn't used), unused helper functions behind `allow(dead_code)`, formatting-only hunks (re-indenting or re-ordering without behavior change), and unrelated files.
- **template_sections_filled:** list the sections the template names. For each, look for it in the description and check for substantive content (not the template's example text).
- **issue_reference_present:** extract the issue number from the URL or bundle. Search the description for `Closes #N`, `Fixes #N`, or `Resolves #N` with the matching N.
- **disclosure_present:** read the repo's contribution-policy line in repo facts or `docs/CONTRIBUTING.md`. If an AI-disclosure policy is named, search the description's Notes for a matching disclosure. If no policy, pass.
- **maintainer_direction_engaged:** read the thread highlights. For each maintainer comment that names a direction, check whether the description addresses it. If no maintainer direction exists, pass by default.
- **title_is_specific:** read the first line of `pr_draft.md` or the bundle's Title. Check for the change named and ideally the mechanism.
- **honest_disclosure_of_shortfall:** compare the plan's named scope to the diff. If the plan names something the diff doesn't deliver, look for that omission in the description's Notes. Present and plain = pass; absent = fail (unless the plan never named it).

## Check execution

<!-- How one check runs against gathered evidence: in what order the
checks execute, what an executor does when evidence for a check is
genuinely absent, and when a check may be graded without re-reading
the whole package. A complete procedure makes two executors grade the
same package the same way. -->
1. Run the seven **required** checks first.
2. Then run the two **preferred** checks.
3. For each check, produce one of three grades: `pass`, `fail`, or `unclear`. `unclear` means the evidence the check names is genuinely absent (a section missing from the package, not a section you did not read). A missing section is `unclear`; a present section that contradicts the check's condition is `fail`.
4. Write one line of evidence per check — the fact or quote that decided it. No grade without a fact.
5. Do not re-read the whole package for each check. Gather all facts for a family once (per the Evidence gathering steps) and grade from those facts.


## Verdict assembly

<!-- How the per-check grades become the final accept or reject:
apply your rubric's verdict rule, state how unclear grades enter it,
and say what gets quoted in the output for the deciding check: the
check whose failure the verdict turned on. When more than one check
failed, your procedure picks which one gets quoted (first failing
required check in rubric order is a fine rule); nothing picks it for
you, so write the rule down. A complete procedure produces the same
verdict from the same grades, every time. -->
1. Take the grades of the seven required checks.
2. If any required check is `fail`, the verdict is `reject`.
3. If any required check is `unclear`, treat it as `fail` (per the rubric's rule), and the verdict is `reject`.
4. If all seven required checks are `pass`, the verdict is `accept`.
5. Preferred checks never change the verdict; report them in the summary for ranking.
6. In the output, quote the deciding check's evidence line verbatim in the JSON block. When multiple required checks fail, the deciding check is the **first one in rubric order** (plan_fidelity before description_matches_diff before test_evidence_present, etc.).
7. In live mode, add any voice-guide rule the draft PR title or description breaks as a bullet in the readable summary above the JSON block.

