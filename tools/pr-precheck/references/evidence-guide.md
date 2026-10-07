# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

**Where it lives:** The plan's diagnosis or "Cause" section, read directly against the exact terminal logs and steps recorded in the `## Repro evidence` block.
**What good looks like:** The plan's stated cause cites and explains the specific error message or behavior the repro evidence actually shows. It does not contradict the provided logs or attempt to diagnose a completely different symptom not present in the reproduction.

## Scope

**Where it lives:** The plan's scope statement, specifically looking for the "in-scope" lists, the "out-of-scope" or "not-in-scope" lines, and the exact files named for modification.
**What good looks like:** The plan establishes a hard boundary. It explicitly names the specific files it will touch and explicitly states what related systems or behaviors it will *not* modify, distinguishing a bounded surgical fix from a drive-by rewrite.

## Executability

**Where it lives:** The approach, implementation steps, or execution order detailed within the candidate plan.
**What good looks like:** The plan details exactly what logic will be added, removed, or changed in the named files. A stranger could read the proposed steps and immediately start writing the code without having to ask the author what they meant or where to put it.

## Test plan

**Where it lives:** The "Test plan" section of the candidate plan, mapped directly against the original steps from the `## Repro evidence` block.
**What good looks like:** The test plan specifies exactly how success will be observed (e.g., running specific unit tests, or re-running the repro steps to see a specific new terminal output). It names observable, concrete outcomes rather than vaguely stating "make sure it works."

## Honesty

**Where it lives:** The risks, unknowns, or deviations sections of the candidate plan.
**What good looks like:** The plan plainly states what the author does not yet know (e.g., "Unknown if this impacts the caching layer") rather than dressing up assumptions as absolute certainty. If deviations occur later, they are explicitly recorded here rather than quietly overwritten.

## Comms

**Where it lives:** The `## Candidate plan comment` section, read against both the `## Thread highlights` (or live thread) and the `## Repo facts` block (specifically the contribution policy and AI-use requirements).
**What good looks like:** The posted comment explicitly acknowledges any active maintainer signals or prior contributors in the thread. It strictly complies with any repository rules stated in the facts block (such as an AI-use disclosure) rather than dropping generic, boilerplate promises.