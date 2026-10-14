# Evidence guide: where evidence lives in a PR package

<!--
THIS IS THE PART YOU WRITE (third week running: the map stays in your
hands). Your tool uses this guide as its map: for every kind of
evidence a rubric check names, this file says WHERE to find it in a PR
package and WHAT GOOD LOOKS LIKE when you do.

The four families below are the harness's failure categories under
the names the eval README uses: plan fidelity = silent-drift, test
evidence = not-tested, diff quality = unreviewable, standards and
comms = standards-wall. A package that fails none of them is a
clear-accept. Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the plan-context block's scope pair and test
  plan, the candidate PR's diff, commits, description, or
  test-evidence section, the repo-facts block's template asks and
  stated policy). In live mode (where in your working copy and on
  GitHub: your plan.md and its deviation notes, your branch's diff,
  your draft title and description, your captured test output, the
  repo's PR template and CONTRIBUTING.md).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("every changed file falls inside the
  plan's stated boundary or a deviation note") over adjectives ("the
  diff is clean").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts three ways: your
procedure says WHEN to gather each family, this guide says WHERE, and
your SKILL.md says the tool reads both. Write the map you wish your
executor had.
-->

## Plan fidelity (harness category: silent-drift)

**Where it lives:**
*   **Eval mode:** The `plan-context` block's scope and deviation notes, read side-by-side with the candidate PR's diff and description.
*   **Live mode:** Your local `plan.md` (including the `## Deviations` section) read side-by-side with the output of `git diff main...HEAD` and the claims made in your draft PR description.

**What good looks like:**
Every single file modified, added, or deleted in the diff is explicitly authorized by the plan or a recorded deviation note. The PR description's summary of changes strictly matches the diff—claiming to implement a feature that isn't in the code, or quietly sneaking in a change not mentioned in the description, constitutes silent drift. An honestly disclosed shortfall (e.g., "I left out X because of Y") is a pass.


## Test evidence (harness category: not-tested)

**Where it lives:**
*   **Eval mode:** The `test-evidence` section of the bundle, read against the plan's stated test plan.
*   **Live mode:** The `test_evidence.md` file containing your captured terminal output, read against the test plan in `plan.md` and the repo's required checks (like `make test-unit` or `make lint`).

**What good looks like:**
The evidence contains raw, observable terminal output or test suite logs proving the before-and-after state. It shows the concrete outcomes of the repo's mandated checks. A simple human assertion like "tests pass" or "checked locally" with no accompanying terminal trace is an automatic failure.

## Diff quality (harness category: unreviewable)

**Where it lives:**
*   **Eval mode:** The candidate PR's unified diff and commit list.
*   **Live mode:** The terminal output of `git diff main...HEAD`.

**What good looks like:**
The diff is highly focused and strictly contains the logical fix. It is entirely free of leftover developer debris, such as `print()` debugging statements, commented-out dead code, unrelated formatting/spacing churn in untouched functions, or drive-by refactoring.

## Standards and comms (harness category: standards-wall)

**Where it lives:**
*   **Eval mode:** The `repo-facts` block (which contains the PR template and stated contributing policy) read against the candidate PR's title and description.
*   **Live mode:** The remote repository's `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` read against your local `pr_draft.md`.

**What good looks like:**
The draft PR explicitly honors the repository's rules. Every required section of the PR template is filled out with relevant content (not just skipped or left as boilerplate). If the repository policy demands an AI-use disclosure, that disclosure is plainly visible in the description.