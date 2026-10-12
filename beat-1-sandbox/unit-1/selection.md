# Unit 1 selection

## Chosen issue

Link: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61

Title: Health check DB probe passes a raw SQL string, which fails under SQLAlchemy 2.x

### Skill verdict (live mode)

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61",
  "checks": [
    {"name": "maintainer_alive", "grade": "pass", "evidence": "last 5 commits all authored by human Aburke225; repo not archived"},
    {"name": "repo_in_use", "grade": "pass", "evidence": "not archived; last push 2026-09-16, within 12 months of 2026-09-23"},
    {"name": "scope_fits_newcomer", "grade": "pass", "evidence": "names api/routes/health.py, bounded fix (wrap 'SELECT 1' in text()), opened by collaborator Aburke225, no design debate"},
    {"name": "not_a_graveyard", "grade": "pass", "evidence": "0 comments, no claim attempts, no maintainer 'blocked' statement"},
    {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; no linked/mentioned PRs found"},
    {"name": "human_filed", "grade": "pass", "evidence": "author Aburke225, type: User, author_association: COLLABORATOR"},
    {"name": "policy_allows_ai_assisted", "grade": "pass", "evidence": "docs/CONTRIBUTING.md has no AI-use policy; silence passes"},
    {"name": "good_first_issue_signal", "grade": "pass", "evidence": "labels include good first issue and tier-1"},
    {"name": "clear_repro_or_acceptance", "grade": "pass", "evidence": "body names api/routes/health.py and the SQLAlchemy 2.x failure mode"},
    {"name": "has_release", "grade": "fail", "evidence": "GET /releases returned 0 releases"}
  ],
  "verdict": "accept"
}

Reflection

Why did you pick this issue?
I picked #61 because it's a bounded backend bug with a clearly named file to change (api/routes/health.py), a concrete fix (wrap the raw SQL string in SQLAlchemy's text() construct), and a tier-1 difficulty label. It fits my interest in backend work and gives me a chance to write a regression test.

What do you expect to be the hardest part?
The hardest part will probably be setting up the local dev environment and reproducing the SQLAlchemy 2.x error outside the test suite.

What are you hoping to learn?
I want to learn how a FastAPI backend is structured, how to write a test that catches the bug, and how to open a pull request.
Run history

    Baseline full run: 12/20. The main problem was maintainer_alive, which rejected four issues (conda, LegalQuants, etc.) because the response sample showed no maintainer comment within 30 days, even though the repos had human commits that same week. repo_in_use also rejected issue-06 for having no published release.

    Edited maintainer_alive to require only a human-authored commit in the last 5 (dropping the response-latency clause) and edited repo_in_use to drop the release requirement. Re-ran --only on the eight disagreements: six flipped to agree with gold.

    Loosened scope_fits_newcomer so that issues opened by a maintainer/collaborator that name specific files are treated as bounded. Added a new required human_filed check to reject issues opened by [bot] accounts. Re-ran --only issue-19,issue-20: both flipped.

    Regression-checked the previously fixed six: all still agreed.

    Final full run with --save-run eval-run.txt: 20/20, all five composition categories full.

Issue analysis

Issue-01 (conda/conda#16475). My rubric said reject; the gold label said accept. The disagreement came from maintainer_alive, which then read: "At least 1 human-authored commit in the last 5 AND at least 1 maintainer first-response in the sample within 30 days of the issue's open date." conda's commit history is plainly alive — five human commits in the week before capture, a release four days before capture — but the maintainer first-response sample showed four of five issues with no maintainer comment at all, and the one that had one took 32.9 days. My check required the response signal as a second, independent gate, so it rejected an active repo. Gold treated conda as clearly alive and accepted the issue. I concluded my check over-weighted response latency as a proxy for repo health and split the two signals: commits alone are now sufficient.
Check rationale

My final maintainer_alive check reads, verbatim:

    "The 'last 5 default-branch commits' line under Repo facts. Count only commits authored by a human (not a [bot] account). Also check the 'archived:' line on the repo line."

Pass condition:

    "At least 1 human-authored commit in the last 5, AND the repo is not archived."

I dropped the maintainer-response requirement after the eval showed it caused false rejections on repos (conda, LegalQuants) that are unmistakably alive by their commit and release activity. My original check conflated two different questions — "is there a human working here?" and "do maintainers respond to every issue?" — and the second question isn't what makes a first issue takeable. Commit activity plus the not-archived flag is a tighter, more honest signal.
Trade-offs

The loosened maintainer_alive is now easier to pass. A repo with one recent commit but zero maintainer engagement on any issue would pass. I accepted that risk because the eval's actual failure mode was the opposite: false rejections on active-but-quiet repos. The dead-repo composition category (3/3 in the final run) still catches the truly abandoned cases via last-push and archived flags, so I don't think I've opened a hole. Similarly, promoting human_filed to a required check risks rejecting an excellent issue that happens to be filed by a bot, but the eval set showed that the one bot-filed issue in the set was genuinely lower quality — a feature request with no labels, no maintainer response, and an incomplete spec ("Logo asset TBD"). If the eval had contained a bot-filed issue that was otherwise excellent, this trade would have gone the other way.