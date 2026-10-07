# Procedure: how this skill grades a plan package

## Read order

1. Read the original GitHub issue to understand the user-reported problem.
2. Read the reproduction evidence (from Unit 2), paying close attention to the terminal logs and the exact point of failure.
3. Read the candidate's proposed fix plan (`plan.md`).
*Note on order: You must read the issue and reproduction evidence before the plan. An executor cannot accurately judge if a plan's diagnosis and scope are correct if they do not first understand the concrete reality of the bug proved by the reproduction steps.*

## Evidence gathering

1. **For `diagnosis`:** Locate the plan's "Diagnosis" section. Extract the stated root cause of the bug and record it next to the actual error trace shown in the reproduction evidence.
2. **For `scope`:** Locate the plan's "Scope" or "Changes" section. Record the exact list of files the author plans to modify, and record the explicit boundary (what the author states they will *not* touch).
3. **For `test`:** Locate the plan's "Test Plan" section. Record the proposed testing steps, specifically noting any mention of unit tests (existing or new) and manual verification steps.

## Check execution

1. Execute the `diagnosis` check first. Compare the gathered root cause to the reproduction logs.
2. Execute the `scope` check second. Verify the gathered file list is explicitly named and bounded.
3. Execute the `test` check third. Verify the gathered test steps include unit testing and manual verification without relying solely on AI approval.
4. If the evidence for any check is genuinely absent (e.g., the plan lacks a Test section), immediately grade that check as `?` (unclear).
5. Grade all checks strictly using the facts recorded during the Evidence Gathering stage without re-reading the entire package from the top.

## Verdict assembly

1. Review the assigned grades for all three required checks.
2. Apply the verdict rule: the final verdict is `accept` (ready) if and only if every required check is a `Pass`.
3. If any required check is a `Fail` or `?` (unclear), the final verdict is `reject` (hold).
4. In the final output JSON, quote the exact text from the plan (or state the explicit absence of text) as the evidence for the deciding check.