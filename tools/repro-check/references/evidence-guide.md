# Evidence guide: where proof lives in a reproduction package
## Environment

**Where it lives:** The "Environment", "System Info", or "Context" sections of the reproduction report.
**What good looks like:** The report lists explicit versions for the operating system and the primary tools/languages required to run the code. If the `repo facts` block indicates a structured bug report template, the report fills in those specific environment fields rather than leaving them blank or stating "N/A".

## Steps

**Where it lives:** The "Steps to Reproduce" section, command-line snippets, or code blocks in the reproduction report.
**What good looks like:** The steps provide exact, executable terminal commands or minimal code required to trigger the bug from a fresh state. A stranger could copy-paste the commands in order without having to guess missing parameters, directories, or setup commands.

## Behavior shown

**Where it lives:** The "Actual Behavior" or "Output" sections of the report, specifically inside code blocks showing terminal logs, stack traces, or error messages.
**What good looks like:** The provided logs or output demonstrate the exact failure, error trace, or panic described in the original issue. The artifact is raw terminal output rather than a human summary (e.g., "it crashed").

## Honesty

**Where it lives:** The contrast between the stated outcome in the report and the provided logs/artifacts.
**What good looks like:** The report's claim strictly matches its evidence. If the author claims to have reproduced the bug, the logs show the exact bug. If the author states they could *not* reproduce the bug, they explicitly state the failure to reproduce and provide the full log of their attempt as proof of a genuine effort, rather than making a baseless assertion.

## Comms

**Where it lives:** The candidate's claim comment read against the issue's comment thread, and the candidate's report read against the repository's `CONTRIBUTING.md` policy in the `repo-facts` block.
**What good looks like:** The claim comment explicitly acknowledges any pre-existing claims or open PRs by other contributors in the thread rather than ignoring them. If the repository has a stated policy (such as a requirement to disclose AI usage), the report/comment explicitly includes that disclosure. The claim comment promises an investigation rather than asserting a guaranteed fix.