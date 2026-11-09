# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request I opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/278

**Branch**

fix/61-sqlalchemy-text-wrapper

## Eval iterations

**Run history**

1. First full run: 15/20. All 5 clear-accepts (pkg-02, 05, 08, 11, 19) failed on `issue_reference_present` because the check demanded a literal `Closes #N` in the description body.

2. Revised `issue_reference_present` to accept any reference form (title `(#N)`, `Closes #N`, `Fixes #N`, `Resolves #N`, or the issue number named in Summary/Issue). Re-ran `--only pkg-02,pkg-05,pkg-08,pkg-11,pkg-19`: 5/5.

3. Canary check on pkg-01 (standards-wall) and pkg-03 (silent-drift): both stayed reject.

4. Full run with `--save-run eval-run.txt`: 20/20, all categories full (clear-accept 7/7, not-tested 4/4, silent-drift 4/4, standards-wall 2/2, unreviewable 3/3).

**Package analysis**

pkg-02 (Textualize/rich#4208). My rubric initially rejected it; the gold label was accept (clear-accept). The disagreement came from `issue_reference_present`, which then required a `Closes #N` line in the description body. pkg-02's PR referenced the issue in its title as `(#4208)` and did not have a separate `Closes` line. The description still clearly tied the PR to issue #4208. I loosened the check to accept any reference form (title `(#N)`, `Closes`, `Fixes`, `Resolves`, or issue number named in Summary/Issue), and pkg-02 flipped to accept. That is exactly the behavior that was failing four other clear-accepts, so fixing the one check fixed five packages at once.

**Check rationale**

My final `issue_reference_present` check reads, verbatim:

> "The PR references the issue it implements by number — anywhere in the title, description, or commit messages. Accepted forms include `Closes #N`, `Fixes #N`, `Resolves #N`, `(#N)` in the title, or the issue number named in a Summary or Issue line. The reference must match the issue number in the URL or bundle's issue context."

I wrote it this way after the first full run, which rejected 5 clear-accepts because the original check's wording ("The description includes a `Closes #<number>` (or equivalent)") was read narrowly by the model as requiring the literal word `Closes`. The explicit list of accepted forms removes that ambiguity and matches what gold actually treats as a valid reference.

**Trade-offs**

The loosened `issue_reference_present` accepts PRs where the issue number appears only in the title or commit messages, not in the description body. A stricter reading — "the description must contain `Closes #N`" — would catch PRs that fail to formally close the issue. I traded that strictness for accuracy on what "this PR is about issue #N" actually means: a title `(#4208)` is unambiguous evidence that the PR is tied to #4208, even without a `Closes` line. A case where this matters: if a contributor wrote "See #4208 for context, this PR also fixes the same code" without a `Closes` line, my check would accept it even if the change doesn't actually resolve #4208. That's a real limitation. The gold set contained no such case; if it had, the check would need to test *resolves*, not just *references*. Since Path Review's template doesn't require `Closes` specifically, and the review process would catch an overbroad claim in practice, the looser read holds up.