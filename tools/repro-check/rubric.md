# Rubric: is this a valid reproduction report?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| env-recorded | Repo facts' "bug reports" line, checked against the report's Environment/Steps sections. | If the repo facts line names specific items, pass if the report contains those items. If there is no structured template, pass if the report states the tool/version used and describes the problem and steps. | preferred |
| steps-complete | Wherever the report shows the reproduction steps and the command(s) run. | Pass if a stranger could re-run the steps without guessing. | required |
| behavior-honestly-demonstrated | Wherever the report shows the exact input/command used, and the actual output. | Pass if EITHER (a) the input/output demonstrates the exact same failure as the issue's trace; OR (b) it explicitly states it could not reproduce the issue, shows the full attempt (env, steps, actual output) as evidence, and does not falsely claim a match. Fail if non-reproduction is an undocumented assertion. | required |
| expected-actual-stated | Expected and actual lines in the repro report. | Pass if the report explicitly states both expected and actual behavior. | preferred |
| conventions-followed | The repo's contribution policy in repo facts, and the issue's comment thread | Pass if the comment complies with stated repo policies (only require AI disclosure if the policy explicitly mandates it) AND acknowledges prior thread context if present. Pass automatically if the thread has no active prior claims. Reject if the comment demands assignment, makes absolute guarantees, ignores actively working contributors, or violates a stated policy. | required |


## Verdict rule

Accept the reproduction report if and only if every check with weight `required` passes; otherwise, if any `required` check fails or lacks sufficient evidence, reject the report. Preferred checks do not alter the accept or reject verdict.