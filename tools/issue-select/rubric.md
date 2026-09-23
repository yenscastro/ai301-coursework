# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| maintainer_alive | The "last 5 default-branch commits" line under Repo facts. Count only commits authored by a human (not a `[bot]` account). Also check the "archived:" line on the repo line. | At least 1 human-authored commit in the last 5, AND the repo is not archived. | required |
| repo_in_use | The "latest release", "last push to any branch", "archived:", and star count on the repo line under Repo facts. | Not archived, AND last push within 12 months of the capture date. | required |
| scope_fits_newcomer | The issue body and the comment thread. | The issue is NOT an umbrella/tracking issue, NOT a pure usage question. The thread does NOT show an unresolved design debate or a maintainer saying the fix touches core internals. If the issue was opened by a maintainer/collaborator and names specific files or approaches, treat it as bounded. | required |
| not_a_graveyard | The comment thread. Count claim comments ("I'll take this", "working on this", "@zulipbot claim") and look for abandoned attempts. | Fewer than 3 abandoned claim attempts on the issue, AND no maintainer statement that the issue is blocked. | required |
| unclaimed | The "this issue: assignees:" and "linked PRs:" lines under Repo facts. | No assignee, AND no open linked PR. | required |
| human_filed | The issue's author line (e.g. "opened by cursor[bot] (NONE)"). | The author is not a `[bot]` account. | required |
| policy_allows_ai_assisted | The "contribution policy" line under Repo facts. | The policy does not outright ban AI-generated contributions. Conditions (disclosure, personal understanding, testing) pass. Silence passes. | required |
| good_first_issue_signal | The issue's labels line. | The issue carries a `good first issue` label OR an equivalent label (`easy`, `help wanted`, `beginner`). | preferred |
| clear_repro_or_acceptance | The issue body. | The body contains either numbered reproduction steps, an acceptance-criteria checklist, or a named file/function to change. | preferred |
| has_release | The "latest release" line under Repo facts. | At least one release is published. | preferred |

## Verdict rule

Accept if **every required check passes**. A single required `fail` or
`unclear` rejects the issue. Preferred checks never change the verdict;
they rank the accepted issues (more preferred passes = higher rank).
Unclear counts as fail for required checks, and as "not passed" for
preferred checks.