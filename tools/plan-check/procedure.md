# Procedure: how this skill grades a plan package

<!--
THIS IS THE PART YOU WRITE, and it is a new kind of part. Weeks 1 and
2, SKILL.md carried a numbered workflow and you only wrote judgment
files. This week the workflow is gone from the frame: SKILL.md says
"execute procedure.md", and these are the operating steps you author.
The machinery is in your hands now.

Your operator swap is the design brief. When your executor stalled
because your rubric said WHAT to decide but not HOW to find the
evidence, that was a procedure gap. This file is where those gaps get
closed: a complete procedure lets someone who has never seen a plan
package before (a groupmate, or the skill itself) grade one exactly the
way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
repro evidence before the plan, and note what behavior it pins down"
is a step; "understand the context" is a wish.
-->

## Read order

<!-- What gets read, in what order, before any check is graded, and
what to note down from each part while reading. A complete procedure
decides the order (issue first? repro evidence first?) and says why
the order matters for the checks that come later. -->
1. Read the issue context first: The title, the body, and the thread highlights. Note the described failure, the expected behavior, and any maintainer statement about the direction of the fix. 
2. Read the repo-facts block next: the contribution policy line, the bug-report template asks, and the latest release / commit state.  Not any AI-use policy or disclosure requirenment, and anything the template demands (version, confirmation, repro steps, expected output).
3. Read the repro-evidence block: The environment, the steps the author ran, and the output they captured. Note the exact error text or wrong value the evidence shows, and the code path it happens in. 
4. Read the plan itself: diagnosis, scope, files to touch, approach, test plan, risks and unknowns, deviations. Note where the plan cites repro evidnece and where it asserts without citing. 
5. Read the plan comment last. Note whether it names the issue's specific mechanism, whether it matches the thread's tone and prior statements, and whether it overstates certainty. 
6. Only after all five parts are read, start grading checks 

## Evidence gathering

<!-- For each evidence family your rubric's checks name, the concrete
gathering move: which part of the package (or, live, which page or
thread location per your evidence guide) to pull the fact from, and
what to record. A complete procedure leaves no check whose evidence an
executor would have to hunt for. -->
For each check in the rubic, the evidence lives as follows:

- **diagnosis_matches_evidence**: quote the plan's stated cause; quote the error or wrong value from teh repro-evidence blcok; check the two describe the same failure. 
- **scope_is_bounded**: quote the plan's "what I'll change" list and its "what I won't" list. Count the surgaces named. If more than one file/module is proposed beyond a file and its tst, note it. 
- **targets_cause_not_symptom**: quote the plan's approach. Quote the code path the repro evidence's stack trace shows. Compare: does the approach modify the named code path, or a downstreamt call?
- **executable_by_stranger**: quote the plan's files-to-touch list. Cound named file paths and named functions. If the plan says "investigates X" rather than "change Y in files Z", note it. 
- **test_plan_observable**: quote the test plan's "expected after fix" statement. Check it names a specific observable (a status code, a returned value, an absence of exception). "Test pass" without a named observanle is the fail signal. 
- **unknowns_are_stated**: quote the risks/unknows section. If the section is missing entirely, that is a fail; if it says "none," that is a pass. 
- **comment_respects_thread**: quote the plan comment's opening and closing. Quote the thread highlights and the contribution policy line. Check for contradiction or uncredited repetition of a maintainer's direction.
- **comment_is_specified**: quote the plan comment's first two sentences. Check for a named mechanism (a function, a files, an error) rather than a general statement of the issue title. 
- **comment_states_uncertainty**: quote the plan comment's connetnebt sentence. Compare with the plan's unknowns section: if unknowns exist and the comment claims certainty, fail. 

In eval mode, everything comes from the bundle; do not fetch. In live mode, gather the issue-side facts from the locations named in `references/evidence-guide.md`, and take the repro evidence from the student's posted repro comment (or the house repro pack, if applicable).



## Check execution

<!-- How one check runs against gathered evidence: in what order the
checks execute, what an executor does when evidence for a check is
genuinely absent, and when a check may be graded without re-reading
the whole package. A complete procedure makes two executors grade the
same package the same way. -->

1. Run required checks first: `diagnosis_matches_evidence`, `scope_is_bounded`, `targets_cause_not_symptom`,  `executable_by_stranger`, `test_plan_observable`, `unknowns_are_stated`, `comment_respects_thread`.
2. Then run preferred checks: `comment_is_specified`, `comment_states_uncertainty`.
3. For each check, produce one of the three grades: `pass`, `fail`, or `unclear`. `unclear` means the evidence the check names is genuinely absent from the package, not that you did not look. If a section the check needs is missing entirely that is `unclear`.
4. Write one line of evidence per check - the fact or quote that decided it. No grade without a fact. 
5. Do not re-read the whole packege for each check. Gather all the evidence for a check's family once (per the Evidence gathering steps above) and grade the check from that. 


## Verdict assembly

<!-- How the per-check grades become the final accept or reject:
apply your rubric's verdict rule, state how unclear grades enter it,
and say what gets quoted in the output for the deciding check. A
complete procedure produces the same verdict from the same grades,
every time. -->

1. Take the per-check grades for the seven required checks. 
2. If an required checks is `fail`, the verdirict is `reject`. 
3. If any required check is `unclear`, treat it as `fail` per the rubric's verdict rule; the verdict is `reject`.
4. If all seven required checks are `pass` , the verdict is `accept`. 
5. Preferred checks never change the verdict. Report them in the summary so accepted plans can be ranked. 
6. In output, quote the deciding chek's evidence line verbatim in the JSON block, and note any voice guide rule the comment broke (in live mode) in the readable summary above the JSON

