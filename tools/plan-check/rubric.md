# Rubric: is this plan ready to post and build from?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis | The plan's diagnosis section read against the issue and reproduction evidence. | The proposed plan addresses the issue raised and identifies the true source of the bug. | required |
| scope | The plan's Scope or Changes section. | Every file the plan changes is explicitly named, and the proposed changes are clearly bounded to the specific issue. An explicit list of things it "does not address" is helpful but not strictly required as long as the planned change is isolated and focused. | required |
| test | The plan's Test section. | The plan specifies concrete, observable steps to verify the fix, such as re-running the reproduction steps to see the expected behavior. It does not need to explicitly promise new unit tests if manual verification of the bug is sufficient, but it cannot be completely empty or vague (e.g., "I'll make sure it works"). | required |

## Verdict rule

Accept (ready) if every required check passes. Any check resulting in unclear/insufficient evidence (?) counts as a fail (hold).