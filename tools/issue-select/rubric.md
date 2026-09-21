# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| maintainer-active | The last 5 default-branch commit dates in the repo-facts block | The repository has at least one commit within the last 12 months. | required |
| unclaimed | The repo-facts block AND the comment thread | Assignees must be "none" AND there must be no OPEN linked PRs (closed PRs are allowed). Ignore comment claims or expressions of interest older than 60 days that did not result in an open PR. | required |
| human-authored | The issue author metadata | The issue must be opened by a human. Reject if opened by an automated bot (e.g., cursor, dependabot, github-actions). | required |
| valid-policy | The issue body | The issue must propose a concrete task (bug fix, documentation, or specific feature). Reject general support questions, user troubleshooting, or "how-to" questions. | required |
| manageable-scope | The issue body and comment thread | The task must have clear boundaries. Highly detailed issues with step-by-step task checklists are EXCELLENT and must pass. Reject vague, undecided design requests. Reject "graveyard" issues (where the comment thread shows multiple previous contributors claiming and abandoning the task over a long period). | required |

## Verdict rule

Accept if every required check passes. Any check resulting in unclear/insufficient evidence counts as a fail.
