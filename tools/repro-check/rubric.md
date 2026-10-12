# Rubric: is this reproduction package ready to post?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here. It ships empty on purpose: the judgment is your
work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the claim comment, the repro report's environment
     record, the artifacts read against the issue's description, the
     repo-facts block) or a location from your
     references/evidence-guide.md. "The report" is not a source; "the
     output excerpt read against the error the issue describes" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (does
     the artifact show the issue's behavior?), never the write-up's
     shape (how many steps it has, how long it is, whether it uses a
     template's headings). Structure-shaped checks are what make
     graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad packages posted. The lecture named the
proof families: the environment is recorded, the steps are complete
and followable, the behavior shown matches the issue (not an adjacent
one), the outcome is stated honestly (an evidenced cannot-reproduce is
a pass, a confident wrong-target is not), and the words respect the
repo's conventions. A rubric that ignores a family will fail eval
packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| environment_recorded | The report's environment section, read against the issue's stated target and the repo's bug-report template. | The report names (a) the OS, (b) the runtime/language versions, and (c) the repo revision. When the issue states a target version and the report runs on a version BEHIND that target with no explanation, fail. Running on the same version, a newer version, or a version ahead of the target passes. Running on a different OS or shell than the issue reports is acceptable if the report shows the issue's behavior reproduced (or, for a cannot-reproduce, explains why the difference matters). | required |
| behavior_matches_issue | The report's output excerpt read against the issue's described behavior. Only applies when the report claims reproduction (outcome_honest says "reproduced"). If the report states cannot-reproduce, this check passes by default and outcome_honest carries the weight. | When the report claims the behavior reproduced, the output shows the same class of failure the issue describes — same exception type, same failing code path, same wrong value — not merely a failure in the same area. Exact string match not required if the failure class and location match. | required |
| outcome_honest | The report's outcome statement. | The report states plainly whether the behavior reproduced, partially reproduced, or did not reproduce. An evidenced cannot-reproduce passes. A report that claims reproduction but whose evidence shows different behavior fails. | required |
| conventions_respected | The repo's contribution policy in the repo-facts block, and the claim and repro comments themselves. | If the policy requires AI-assistance disclosure, the comments disclose. If the policy bans AI-generated contributions outright, the package fails. Silence on policy passes. | required |
| issue_referenced | The claim comment. | The claim names the specific issue (by title, number, or a one-line restatement of the bug) and states what the author will do next. | required |
| promise_not_assertion | The claim comment. If the package includes a repro report section, the claim comment may assert reproduction backed by that report. If the package has no repro report (a claim-only draft), the claim comment must use future tense. | If a repro report is present: the claim comment does not promise a fix or a date, and any asserted reproduction is backed by the report's evidence. If no repro report: the claim comment names the issue, promises the report to come, and asserts no results it does not have. | required |
| repro_self_contained | The repro report. | Every artifact the report depends on is either pasted inline, referenced by a public URL, or reproducible from public sources. No step relies on "my company's monorepo," "our internal config," or any artifact the reader cannot obtain. | required |

## Verdict rule

Accept if **every required check passes**. A single required `fail` or
`unclear` rejects the package. Preferred checks never change the
verdict; they rank accepted packages (more preferred passes = higher
rank). Unclear counts as fail for required checks, and as "not passed"
for preferred checks.