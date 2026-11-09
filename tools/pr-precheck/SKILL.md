---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

<!--
THIS IS THE PART YOU WRITE, and it is the last one: the frame itself.
Weeks 1 through 3 handed you a working SKILL.md and you filled the
files behind it; this week the frame ships as headings, and you write
what it says. The frontmatter above and the section headings below are
fixed (CONTRACT.md's layout rule); the instructions under each heading
are yours. Write instructions to the tool, in the imperative, the way
weeks 1-3's frames spoke to you: what to read, in what order, what to
refuse, what to emit. Your executor in the rotation is the test: a
frame gap they hit (cannot tell what the tool reads, or how a verdict
gets assembled) is a missing sentence here.

One section is not yours: the JSON schema in "Verdict and output" is
reproduced from CONTRACT.md verbatim and may not be altered. Your
words decide everything around it.
-->


## The question

<!-- State, in your words, the one question this tool answers and what
a PR package is: what artifacts it contains and what they are read
against. CONTRACT.md fixes the question; your frame has to say it so
the tool cannot wander into grading something else. -->

This tool answers one question about one PR package: is this pull request ready to submit? A PR package is a candidate pull request - its title, description, commit list, diff, and test evidence - read against the plan it claims to implement and the issue that plan belongs to. The tool never aswers a different question, never grades more than one package per run, and never answers from gut feel. It exectes the components in thisdirectory in the order its procedure.md specifies. 



## Inputs and modes

<!-- Define both modes. Live mode: name every input (your plan.md with
its deviation notes, your branch's diff, your draft PR title and
description, your test evidence, your issue), where each comes from,
and what a house-chain student reads instead. The branch's diff is
everything the branch changes relative to the repo's default branch:
`git diff main...HEAD` (three dots), run from the working copy, is
the command that produces it; name the source that concretely. Eval
mode: state that the bundle is the whole world, nothing is fetched,
and every check runs with the full verdict rule. -->

**Live mode:** grade the student's own branch before the PR goes out. Read, in order:
- The issue URL passed on the commande line; fetch the issue body, the thread highlights, the repo's `.github//PULL_REQUEST_TEMPLATE.md`, and `docs/CONTRIBUTING.md` (or whichever contributing file the repo ships).
- The student's `plan.md` in the working copy, including the `## Deviations` section. 
- The branch diff, produced by `git diff main..HEAD` from the working copy (three dots: changes the branch adds relative to main).
- The student's `pr_draft.md` - the PR title on the first line, description below. 
- The student's `test_evidence.md` - before/after repro output and the repo's checks. 

**Eval mode:** the bundle is the whole world. The bundle text contains the issue context, repo facts, plan context, candidate PR (title, description, commits, diff, test evidence). Do not fetch anything. Grade every check, apply the full verdict rule. Ignore `scope.md` and `voice-guide.md` entirely. 

## The scope seam (live mode only)

<!-- Tell the tool when to read scope.md, what to do with the rules it
finds there, what to refuse, and what to do when the scope's repo line
is an unfilled placeholder. State that eval mode ignores scope.md
entirely. CONTRACT.md names the required behavior; your frame has to
instruct it. -->
Read `scope.md` fird in live mode. Confirm the issue's repo matches the `Repo:` line. If the line is still a bracketed placeholder (`<ORG>/<PATH-REVIEW-REPO>`), stop and tell the student to fill it; do not guess. If the issue's repo is outside the scope, refuse to grade. Eval mode ignores this file. 

## The voice seam (live mode only)

<!-- Tell the tool when to read voice-guide.md, which outgoing text it
gates (the PR title and description), how to report a broken rule, and
why it never changes the verdict on its own. State that eval mode
ignores it entirely. -->
Read `voice-guide.md` in live mode. Hold the student's PR title (`pr_draft.md` first line) and description against the rules. Report every rule the draft breaks, quoting the rule. The voice guide never changes the veridict on its own unless a rubic check reads it. Eval mode ignores this file. 


## Component reads

<!-- Tell the tool how the pieces connect: rubric.md defines the
checks and the verdict rule, references/evidence-guide.md maps where
each evidence family lives, procedure.md is executed as written. Say
what the tool does when the procedure is silent on a step (report the
gap, never improvise around it) and what it does when rubric.md or
procedure.md has no content (the refusal rule, stated as an
instruction). -->
Read `rubic.md` for the checks and the verdict rule. Read `references/evidence-guide.md` for where each family of evidence lives in a PR package. Execute `procedure.md` as written - the read order, the evidence gathering for each family, the check execution, and the verdict assembly.

If `procedure.md` is silent on a step, report the gap in the summary rather than inventing one. 

**Refusal rule:** if `rubic.md` has no filled-in checks, or `procedure.md` has no filled-in steps, or `SKILL.md` has no filled-in frame, stop and say so. Do not grade with an empty tool. 

## Verdict and output

<!-- State the binary verdict space (accept means submit, reject means
hold) and instruct the tool to end its reply with the fenced JSON
block below, valid and last, with nothing after it. The schema is
CONTRACT.md's, verbatim; do not edit it. -->

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

<!-- Write the standing rules the tool grades under: evidence first,
grade the thing not the polish, the rubric decides, the procedure
decides how, and how unclear grades are treated when the rubric's
verdict rule is silent. CONTRACT.md states each as a guarantee; your
frame has to make them instructions. -->
**Evidnece first.** No  check is graded without naming the fact or quote that decided it. "Looks fine" is not evidence. 
**Grade the thing, not the polish.** A terse complete PR can be ready; a beautiful confident one can be hiding drift. Every check reads the artifact itself, not formatting. 
**The rubic decides.** If a check passes by its stated condition but feels wrong, it still passes. The fix belongs in the rubic, not in the run. 
**The procedure decides how.** Follow `procedure.md` as written; report it gaps rather than papering over them. 
**Unclear degaults to fail.** An unverifiable claim is failling claim. A PR you cannnot verify from the package is not ready to submit.