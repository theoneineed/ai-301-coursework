# Procedure: how this tool grades a PR package

## Read order

1. Read the `plan.md` (including the `## Deviations` section) to establish the approved baseline of what the fix is supposed to do.
2. Read the branch's diff to see exactly what code was added, modified, or removed.
3. Read the test evidence to see the raw terminal outputs and check results.
4. Read the draft PR title and description.
5. Read the target issue and the repository's `CONTRIBUTING.md` / PR template.

*Note on order: The plan must be read first because it is the contract. You cannot judge if the diff drifted, or if the description is honest, without first establishing the exact boundaries approved in the plan.*

## Evidence gathering

1. **For `plan-fidelity`:** Extract the explicit list of files and logical changes from the plan and its deviation notes. Place this side-by-side with the actual changed files and hunks in the diff.
2. **For `test-evidence`:** Extract the testing steps outlined in the plan. Place them side-by-side with the actual terminal outputs, before-and-after results, and CI check statuses provided in the test evidence.
3. **For `diff-quality`:** Scan the diff specifically looking for leftover print statements, commented-out code, formatting hunks unrelated to the core logic, or unresolved merge conflicts.
4. **For `standards-wall`:** Extract the required sections from the repository's PR template and any AI-use disclosure rules from the contributing policy. Place them side-by-side with the draft PR description to ensure every required box is checked and section filled.


## Check execution

1. Execute the `plan-fidelity` check first. Compare the diff against the plan. If the diff touches files not mentioned, or omits files that were promised without a deviation note, grade it `fail`.
2. Execute the `test-evidence` check. If the test evidence only contains assertions (e.g., "tests passed") rather than observable raw terminal output, grade it `fail`.
3. Execute the `diff-quality` check. If unrelated debris is found in the diff, grade it `fail`.
4. Execute the `standards-wall` check. If the description ignores template requirements or omits required AI disclosures, grade it `fail`.
5. If the evidence for any check is genuinely absent from the package, grade that check as `unclear` (`?`).
6. Grade all checks strictly using the side-by-side comparisons gathered in the previous stage without re-reading the entire package.

## Verdict assembly

1. Review the assigned grades for all required checks.
2. Apply the verdict rule: the final verdict is `accept` (ready to submit) if and only if every required check is a `pass`.
3. If any required check is a `fail` or `unclear` (`?`), the final verdict is `reject` (hold).
4. In the final output JSON, if the package is rejected, quote the exact line of missing evidence, debris, or mismatched scope from the **first failing required check** (in rubric order) as the deciding evidence.