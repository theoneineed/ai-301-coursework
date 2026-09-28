# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

## Selected issue

**Issue link**
https://github.com/codepath/pathreview-ai301-fa26-s1/issues/1

**Verdict output**

```json
    {   
      "item": "[https://github.com/codepath/pathreview-ai301-fa26-s1/issues/1](https://github.com/codepath/pathreview-ai301-fa26-s1/issues/1)",
      "checks": [
        {"name": "maintainer-active", "grade": "pass", "evidence": "Last commit 2026-09-16, repo not archived"},
        {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; no open linked PR; one classmate claim comment (2026-09-26) ignored per Path Review house rule"},
        {"name": "human-authored", "grade": "pass", "evidence": "author Aburke225, type: User"},
        {"name": "valid-policy", "grade": "pass", "evidence": "Concrete bug fix: `_check_skip()` queries with a string instead of the model class"},
        {"name": "manageable-scope", "grade": "pass", "evidence": "Named files, 4-6h estimate, one classmate already posted a working repro"}
      ],
      "verdict": "accept"
    }
```
---

## Eval iterations

**Run history**

16/20: Initial baseline run. The manageable-scope check rejected valid documentation tasks because it loosely penalized lengthy descriptions.
17/20: I changed the human-authored check to explicitly reject automated bots (e.g., cursor, dependabot) which correctly flipped issue-20 from accept to reject. I also updated the unclaimed check wording to "ignore comment claims older than 60 days that did not result in an open PR," which flipped issue-12 to accept.
19/20: To fix false rejections on scope, I changed the manageable-scope pass condition to explicitly state: "Accept bug fixes, detailed documentation tasks with checklists... Reject 'graveyard' issues where multiple contributors repeatedly claimed and abandoned the task". This specific rubric edit flipped issue-01 and issue-15 to correctly match their gold labels. This final score matches the eval-run.txt file.

**Issue analysis**
*Issue ID: issue-01
*Gold Label: Accept
*Rubric Verdict: Accept
*Reasoning: In my initial 16/20 run, my rubric rejected issue-01 because the manageable-scope check mistakenly penalized it for having a long description with multiple subsections. To fix this, I changed the manageable-scope pass condition from a generic "must have small boundaries" rule to explicitly state: "Accept bug fixes, detailed documentation tasks with checklists, and concrete feature requests". After this exact rubric edit, the evaluation correctly flipped the verdict to Accept, matching the gold label without punishing the issue for its detailed formatting.

**Check rationale**

| manageable-scope | The issue body and comment thread | Accept bug fixes, detailed documentation tasks with checklists, and concrete feature requests that have a clear use-case. Reject: (1) vague, undecided design requests; (2) "graveyard" issues where multiple contributors repeatedly claimed and abandoned the task; (3) massive architectural rewrites and tracking epics. | required |

This check is designed to prevent the LLM from conflating "lengthy or detailed descriptions" with "massive scope." It ensures that contributors are protected from cursed "graveyard" threads where multiple people have abandoned the issue, while still welcoming well-documented bug fixes and feature enhancements.

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
