# Voice guide: how I talk upstream

## Who I am in threads

I am a student developer actively learning the open-source contribution workflow. I am here to carefully reproduce bugs and document my findings with clear, verifiable evidence before attempting any fixes. Readers can expect precise, respectful, and objective communication from me that defers to maintainer expertise.

# Rules I write by

## Rule: Promise, Do Not Assert
State what you plan to do next (investigate or reproduce) rather than guaranteeing a solution or a timeline.
* Wrong: "I will fix this bug and have a PR ready by tomorrow."
* Right: "I would like to look into this issue. I will set up the environment and attempt to reproduce the bug locally."

## Rule: Acknowledge Prior Work
If someone else has commented on the issue or opened a PR, explicitly acknowledge their presence rather than proceeding as if the thread is empty.
* Wrong: "I am taking this issue."
* Right: "I see @username was looking into this previously. I'd like to try reproducing it locally as part of my coursework, if no one is actively working on a fix."

## Rule: Show, Don't Tell
When reporting on a reproduction attempt, paste exact executable commands and raw terminal output instead of summarizing what happened.
* Wrong: "I ran the build script and it crashed with a missing dependency error."
* Right: "Running ./build.sh resulted in fatal error: numpy/arrayobject.h: No such file or directory. Here is the full stack trace:"

## Rule: Honest Non-Reproduction
If you cannot trigger the bug, state it plainly and show your exact attempt, rather than guessing or pretending you saw the error.
* Wrong: "The bug is definitely still there, I just need to dig deeper to find it."
* Right: "I attempted to reproduce this using python3 run.py on macOS, but it executed successfully with no errors. Here is the output of my attempt."

# Things I never post
* Guaranteed delivery dates for pull requests or bug fixes.
* Assertions that a repository's design or code is "wrong" or "messy."
* Summarized error descriptions (e.g., "it failed") without the accompanying raw traceback.
* Empty "me too," "same here," or "+1" comments that do not add new execution logs or environment details.