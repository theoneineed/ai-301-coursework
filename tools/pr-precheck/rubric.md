# Rubric: is this pull request ready to submit?

<!--
THIS IS THE PART YOU WRITE (fourth week running; this is the rubric's
final form in the sandbox). Your frame in SKILL.md executes whatever
checks you define here, via your procedure.md. It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the diff read against the plan's scope, the test
     evidence read against the plan's test plan, the description read
     against the diff, the repo-facts block's template asks) or a
     location from your references/evidence-guide.md. "The PR" is not
     a source; "the diff's changed files read against the plan's
     stated boundary" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself
     (does the diff fall inside the plan plus its deviation notes? is
     the claimed evidence observable?), never the write-up's shape
     (how long the description is, how many commits there are).
     Structure-shaped checks are what make graders disagree with
     themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (submit) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. State the `unclear`
   treatment explicitly: the frame here is YOUR SKILL.md, so a rubric
   that stays silent is only covered if your frame's grading
   discipline says what happens (the contract's own default is that
   an unverifiable claim fails).

Cover what actually gets bad PRs submitted. The failure families the
lecture named ARE the harness's scoring categories, same names as the
eval README: silent drift (the diff silently does more or less than
the posted plan, or the description claims fidelity the diff
contradicts), not tested (the evidence proves nothing observable, or
the repo's own checks were never run), unreviewable (debris or
unrelated hunks bury the change), and standards wall (the repo's
stated template and disclosure asks are ignored). Your evidence
guide's four headings map onto these one to one (plan fidelity =
silent drift, test evidence = not tested, diff quality =
unreviewable, standards and comms = standards wall), and the category
floor is scored on exactly these names plus clear accept. A rubric
that ignores a category will fail the eval packages built around
that category. And remember the honest-outcome
rule, fourth week running: a PR that honestly discloses a shortfall
can be ready; a rubric that equates "less than everything" with
"hold" fails the set.
-->


## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| plan-fidelity | The diff read against the plan and its deviation notes. | Passes if the diff implements exactly what the plan asked for. It must contain no silent additions, unrelated changes, or omissions. An honestly disclosed shortfall or deviation documented in the plan passes. | required |
| test-evidence | The test evidence read against the plan's stated test plan. | Passes if the evidence provides observable, raw terminal output or test logs proving the core fix works (e.g., a before-and-after trace or a passing test suite). Do not fail the package for omitting boilerplate linter/formatter outputs as long as the fix itself is visibly proven. An honestly disclosed failing check also passes. | required |
| diff-quality | The branch's diff. | Passes if the change is reviewable and free of debris. It must not contain leftover print statements, commented-out debug code, or unrelated formatting hunks. | required |
| standards-wall | The draft PR description read against the repo's PR template and contributing policy. | Passes if the PR description fills out all required template sections and adheres to the repo's stated rules, including explicit AI-use disclosures. | required |

## Verdict rule

Accept if every required check passes. Preferred checks never change the verdict. Unclear (`?`) or missing evidence counts as a fail.