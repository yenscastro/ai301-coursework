# Rubric: is this plan ready to post and build from?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here (via your procedure.md). It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the plan's scope statement, the test plan read against
     the repro evidence's steps, the plan comment read against the
     thread highlights, the repo-facts block) or a location from your
     references/evidence-guide.md. "The plan" is not a source; "the
     plan's stated cause read against what the repro evidence shows"
     is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (is
     this one bounded change? could a stranger start executing it?),
     never the write-up's shape (how many sections it has, how long it
     is, whether it uses headings). Structure-shaped checks are what
     make graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad plans posted. The lecture named the
failure families: the diagnosis ignores or contradicts the reproduced
evidence, the change is unbounded (scope creep), the plan targets the
symptom while the evidence points at the cause, a stranger could not
start executing it, the test plan proves nothing observable, the
unknowns are dressed up as certainty, and the comment ignores what the
thread or the repo's stated conventions ask. A rubric that ignores a
family will fail eval packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis_matches_evidence | The plan's stated cause (diagnosis section), read against the repro evidence block and the issue body. | The plan names a cause that is consistent with what the repro evidence shows — same error, same code path, same failing behavior. If the plan says "X is wrong," the repro evidence must show X behaving wrong. A diagnosis that contradicts the repro (e.g. blames a different function than the one in the stack trace) fails. | required |
| scope_is_bounded | The plan's scope section: what it will change, what it won't. | The plan names one bounded change with an identifiable surface (one function, one module, one file plus its test). It explicitly excludes things it won't touch. A plan that proposes a refactor "while we're in there," or lists unrelated fixes, fails. | required |
| targets_cause_not_symptom | The plan's approach section, read against the diagnosis and the repro evidence. | The proposed change addresses the cause the diagnosis names, not a symptom downstream of it. If the evidence shows the failure originates in call A but the plan patches call B's return value, it fails. | required |
| executable_by_stranger | The plan's files-to-touch list and approach section, read as if you were a contributor who has never seen the codebase. | The plan names the exact file(s) and function(s) to change, and describes the change concretely enough that a stranger could open the file and start. A plan that says "investigate the health module" without naming the file fails. | required |
| test_plan_observable | The plan's test plan, read against the repro evidence's steps. | The test plan re-runs the repro steps (or a strict equivalent) and states what the author expects to see after the fix — a specific observable outcome (a 200 instead of 503, a value instead of an exception). "Tests pass" or "verify it works" without a named observable fails. | required |
| unknowns_are_stated | The plan's risks and unknowns section. | The plan names at least one thing it is not certain about, or states plainly that there are none. Either is a pass. A plan that presents every step as certain when the diagnosis itself is inferential fails. | required |
| comment_respects_thread | The plan comment, read against the thread highlights block and the repo-facts block's contribution policy. | The comment does not contradict an earlier maintainer statement in the thread, and does not propose a direction the thread has already declined. If the repo's policy requires AI disclosure for contributions, the comment discloses. If the thread already named the fix, the comment does not present the same fix as its own discovery. | required |
| comment_is_specific | The plan comment. | The comment names the issue's specific bug (by mechanism, not just number) and the concrete approach, in the author's own words. Boilerplate or "same approach as above" fails. | preferred |
| comment_states_uncertainty | The plan comment. | Where the plan has unknowns, the comment does not overstate certainty. A comment that promises a completed fix ("this will resolve X") when the plan lists unknowns fails. | preferred |


## Verdict rule

<!-- State how the grades above combine into accept or reject, and how
unclear is treated. Example shape (write your own): "accept if every
required check passes; preferred checks never change the verdict;
unclear counts as fail." -->

Accept if **every required check passes**. A single required `fail` or `unclear` rejects the plan. Preferred checks never change the verdict; they rank accepted plans (more preferred passes = higher rank).
Unclear counts as fail for required checks, and as "not passed" for preferred checks.