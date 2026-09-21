# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/zxcalc/zxlive/issues/555

**Verdict output**

```json
{
  "issue_id": "issue-04",
  "verdict": "accept",
  "reason": "The issue proposes a concrete bug fix/feature enhancement with a clear, localized scope, has no conflicting assignments or open PRs, is authored by a project collaborator, and fits cleanly within local development capabilities."
}
```
---

## Eval iterations

**Run history**

Initial baseline run scored 16/20 agreement, struggling with strict scope interpretations on detailed documentation and feature requests.
Intermediate run scored 17/20 after introducing bot author filtering and clarifying abandoned claim rules.
Final run scored 19/20, meeting all category floors and passing the 18/20 bar. The final agreement score matches the entry in eval-run.txt.

**Issue analysis**
Issue issue-01 (Accept). My rubric initially rejected it because it contained a lengthy description with multiple subsections, causing the manageable-scope check to flag it as too large. After refining the check to explicitly state that detailed instructions and checklists for a single task are acceptable, the rubric correctly matched the gold label of accept.

**Check rationale**

| manageable-scope | The issue body and comment thread | Accept bug fixes, detailed documentation tasks with checklists, and concrete feature requests that have a clear use-case. Reject: (1) vague, undecided design requests; (2) "graveyard" issues where multiple contributors repeatedly claimed and abandoned the task; (3) massive architectural rewrites and tracking epics. | required |
This check is designed to prevent the LLM from conflating "lengthy or detailed descriptions" with "massive scope." It protects contributors from cursed graveyard threads while welcoming well-documented bug fixes.

**Trade-offs**

Making the scope check too rigid causes valid localized enhancements to be falsely rejected as open-ended design discussions, while making it too loose allows umbrella tracking issues to slip through. This trade-off was resolved by explicitly greenlighting concrete features with clear use-cases while maintaining a strict ban on meta-issues and multi-contributor graveyard threads.

---

## Selection rationale

**Selection rationale**

1. The issue has a well-bounded, relatively small scope and clear acceptance criteria, fitting neatly into available development time without requiring complex overarching system changes.
2. The verdict correctly identified that the task is self-contained and actionable. I weighed the clarity of the problem description and the lack of conflicting pull requests against the overall codebase size.
3. Because of the Path Review house rules treating the class environment as collaborative, the anticipated difficulty in claiming it is low, and course credit attaches to opening the pull request regardless of merge status.

---

Related paths: `eval-run.txt` in this directory; skill's files in
`tools/issue-select/`.
