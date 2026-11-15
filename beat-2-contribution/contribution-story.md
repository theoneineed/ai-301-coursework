# Contribution: Defect: issue with reading files due to a bug in pm.vis.Subplot file

Path: `beat-2-contribution/contribution-story.md`


**Contribution Number:** 1
**Student:** Nabin Chapagain
**GitHub Username:** theoneineed
**Issue:** https://github.com/cdslaborg/paramonte/issues/52
**Pull Request:** https://github.com/cdslaborg/paramonte/pull/53



---

## Toolkit
**Unit 5: Manual issue discovery in ParaMonte instead of running issue-scout**

I did not install or run `issue-scout` for this contribution. Instead, I uncovered a real bug while using ParaMonte (`cdslaborg/paramonte`) in MATLAB R2025b for my own simulation and visualization workflow on macOS. Because I already understood the package and its setup, I chose to report and fix that bug directly rather than onboarding onto an unfamiliar repository from an automated search scan.

**Unit 6: Manual root-cause analysis and upstream communication without voice-guard**

I did not set up or use `voice-guard` or a repo-memory file during this phase. I manually inspected the ParaMonte MATLAB source files, traced the class-parsing failure in `Subplot.m` to unescaped Doxygen comment markers, and wrote the issue report and pull request descriptions myself in plain technical prose.

**Unit 7: Manual local verification and PR submission without ship-gate**

I did not install or run `ship-gate` or create a `ship-manifest.md` file. I verified my `Subplot.m` fix locally in MATLAB R2025b by re-running the failing chain-reading functions, then submitted PR #53 manually; when I later bundled additional fixes into a second pull request (PR #54), skipping a pre-submission scope check led me to combine unrelated changes in one PR.

**Unit 8: Manual maintainer follow-up and branch maintenance without pr-monitor**

I did not install or use `pr-monitor` or an automated response playbook. I read maintainer `@shahmoradi`'s feedback directly on GitHub after PR #53 merged and PR #55 split out my `getppm.m` fix, and I manually merged `main` back into my fork branch so the remaining open PR reflected only the unmerged C++ MEX changes.

---

## Why I Chose This Issue

**Claim Comment:** Not applicable — I discovered and filed the issue myself at https://github.com/cdslaborg/paramonte/issues/52 rather than claiming an existing issue from the AI301 Issue Selection site.

I discovered this issue organically while running ParaMonte for my own Monte Carlo simulation and post-processing work in MATLAB R2025b on macOS, rather than finding it through `issue-scout` or the AI301 Issue Selection site. When I tried to read and plot simulation outputs using `readChain` and `readSample`, the visualization step crashed inside `pm.vis.Subplot`. Choosing a real bug I had already reproduced in software I actively used meant I could focus immediately on diagnosing and fixing the root cause instead of spending time learning a brand-new codebase from scratch.

Looking honestly at my tool workflow, skipping `issue-scout` worked well for issue selection because personal domain familiarity let me isolate the `Subplot.m` bug quickly. However, working entirely without automated pre-PR checks later showed a downside when I opened a follow-up pull request (PR #54) that bundled multiple unrelated fixes together instead of keeping every change isolated like PR #53.

**Toolkit**

- Tool: Manual discovery in my own ParaMonte workflow (did not use `issue-scout`)
- What it produced: Direct identification and filing of Issue #52 (`pm.vis.Subplot` file-reading/visualization defect) on `cdslaborg/paramonte`
- Difficulties: None for finding the initial bug, though bypassing structured scouting and scoping habits later led me to bundle unrelated follow-up fixes into PR #54
- What changed: Nothing in `issue-scout`; I kept PR #53 focused strictly on `Subplot.m` and took away the process rule to isolate every separate bug into its own PR

---

## Understanding the Issue
**Problem Description**

Post-sampling read utilities in ParaMonte's MATLAB interface (`readChain`, `readSample`, and related read functions) fail during the visualization phase because MATLAB cannot recognize `pm.vis.Subplot` as a valid class definition.

**Expected Behavior**

Calling `readChain` or `readSample` on completed ParaMonte output files should read the chain/sample data from disk and initialize `pm.vis.Subplot` cleanly so downstream visualization classes (such as `pm.vis.Subplotheatmap`) can generate plots without errors.

**Current Behavior**

ParaMonte opens and reads the output file from disk, but as soon as the read function invokes `pm.vis.Subplotheatmap`—which in turn calls `pm.vis.Subplot`—MATLAB throws an error stating that it cannot find or recognize the `pm.vis.Subplot` function/class.

**Affected Components**

- `src/matlab/main/+pm/+vis/Subplot.m` (superclass for ParaMonte MATLAB visualization objects, specifically lines 10–19 and lines 289–298)
- `src/matlab/main/+pm/+vis/Subplotheatmap.m` and post-sampling read functions (`readChain`, `readSample`) that depend on `pm.vis.Subplot`

**Toolkit**

- Tool: Manual code inspection in MATLAB R2025b (no automated analysis skill used)
- What it produced: Traced the failure from `readChain`/`readSample` -> `pm.vis.Subplotheatmap` -> `src/matlab/main/+pm/+vis/Subplot.m`, pinpointing Fortran-style `!>` Doxygen markers preceding `classdef`
- Difficulties: The runtime error initially pointed to `pm.vis.Subplotheatmap` not finding `pm.vis.Subplot`, which required inspecting `Subplot.m` directly to see why MATLAB rejected the class file
- What changed: Nothing in course tools; documented the exact file path and line numbers (`line 10` and `line 289`) directly in Issue #52

---

## Reproduction Process
**Environment Setup**

- **Hardware & OS:** Apple MacBook (Apple Silicon M1 Pro), macOS 26 Tahoe
- **Runtime:** MATLAB R2025b
- **Project Setup:** Downloaded the latest stable release of `cdslaborg/paramonte` from GitHub and built the package locally from source using the build script/binary rather than relying solely on a pre-packaged app bundle. During local setup and testing on macOS 26 Tahoe, I also encountered Xcode C MEX compilation friction and a secondary property typo in `getppm.m`, which I worked through locally while validating the full sampling-to-plotting pipeline.

**Steps to Reproduce**

1. Build and load the stable ParaMonte MATLAB library in MATLAB R2025b on macOS (Apple Silicon).
2. Run a ParaMonte sampling simulation (or load an existing output chain file) and call `readChain` or `readSample` with visualization enabled.
3. Observe the execution failure when `pm.vis.Subplotheatmap` attempts to instantiate `pm.vis.Subplot`: MATLAB fails to locate/parse `pm.vis.Subplot` because `!>` Doxygen comment lines precede `classdef Subplot < pm.vis.axes.Axes` (line 192).

**Reproduction Evidence**

- **Commit showing reproduction:** Reproduced directly in my local MATLAB R2025b build and documented in https://github.com/cdslaborg/paramonte/issues/52 (fixed in commit `a6d7c76` via https://github.com/cdslaborg/paramonte/pull/53)
- **Screenshots/logs:** Screenshot of the MATLAB error attached directly to Issue #52 (`https://github.com/cdslaborg/paramonte/issues/52`), accompanied by the reported symptom: *"The read functions could not be completed as it would open the file, read the file but moving onto the visualization aspect of read function, it would call pm.vis.Subplot from subplotheatmap function but matlab could not find the pm.vis.subplot function."*
- **My findings:** In MATLAB syntax, `%` is the comment character while `!` is a shell-escape operator. Because `Subplot.m` contained Fortran-style `!>` Doxygen comment blocks starting at line 10 (before `classdef Subplot < pm.vis.axes.Axes` on line 192) and again at line 289, MATLAB treated the `!>` lines as non-comment executable statements and refused to parse the `classdef` block.

**Toolkit**

- Tool: Manual reproduction in MATLAB R2025b (did not use `repro-check`)
- What it produced: Confirmed reproducible crash in `readChain`/`readSample` and isolated the `!>` syntax conflict in `Subplot.m`
- Difficulties: None met during reproduction of `Subplot.m`
- What changed: Nothing in course tools

---

## Solution Approach
**Analysis**

`src/matlab/main/+pm/+vis/Subplot.m` mixed MATLAB comment markers (`%>`) with Fortran-style Doxygen markers (`!>`). While Doxygen recognizes both prefixes, MATLAB requires `classdef` to be the first executable statement in a class file and treats `!` as a shell escape rather than a comment. Converting the `!>` blocks to `%>` preserves Doxygen documentation generation while making the file valid MATLAB syntax.

**Proposed Solution**

Replace every `!>` Doxygen comment prefix in `src/matlab/main/+pm/+vis/Subplot.m` (lines 10–19 and 289–298) with `%>`. I deliberately left the documentation wording, Doxygen tags (`\note`, `@ref`), and all class methods and properties untouched so the fix remained minimal, safe, and easy to review.

**Implementation Plan**

1. Open `src/matlab/main/+pm/+vis/Subplot.m` and replace `!>` with `%>` in the header note block (lines 10–19).
2. Replace `!>` with `%>` in the second note block (lines 289–298) in the same file.
3. Re-run `readChain` and `readSample` in MATLAB R2025b to verify that `pm.vis.Subplot` loads cleanly and plots render.

*(Deviation note: PR #53 followed this exact 3-step plan and merged cleanly. Afterward, while testing additional parts of the macOS build, I opened a separate pull request—PR #54—where I deviated from single-issue scoping by bundling a `getppm.m` typo fix with CMake C++ flags and a new `pm_sampling.cpp` MEX file.)*

**Toolkit**

- Tool: Manual source analysis and planning (did not use `plan-check`)
- What it produced: A targeted 2-block comment-prefix replacement plan in `Subplot.m`
- Difficulties: None met for PR #53
- What changed: Nothing in course tools

---

## Testing Strategy
Because `cdslaborg/paramonte` did not run an automated MATLAB CI suite on the pull request, I verified the change using direct before-and-after execution checks in MATLAB R2025b on macOS:

1. **Before fix:** Running `readChain` and `readSample` on ParaMonte output files failed immediately when `pm.vis.Subplotheatmap` called `pm.vis.Subplot`, throwing an undefined class/function error.
2. **After fix:** After changing `!>` to `%>` on lines 10–19 and 289–298 of `Subplot.m`, MATLAB parsed `classdef Subplot < pm.vis.axes.Axes` cleanly, and re-running `readChain` and `readSample` completed without errors and rendered the expected heatmap/subplot figures.
3. **Diff inspection:** Checked `git diff` prior to opening PR #53 (+21/-21 lines in a single file) to confirm no unintended edits were included.

**Toolkit**

- Tool: Manual MATLAB R2025b execution and `git diff` review (did not use `pr-precheck`)
- What it produced: Before/after runtime confirmation that `readChain`, `readSample`, and `pm.vis.Subplot` execute cleanly
- Difficulties: No automated MATLAB test runner was configured upstream, requiring manual verification
- What changed: Nothing in course tools

---

## Implementation Notes
**2025-10-12**

While running ParaMonte in MATLAB R2025b on my MacBook (Apple Silicon M1 Pro, macOS 26 Tahoe), `readChain` and `readSample` crashed during plotting. I traced the error to `!>` Doxygen comment blocks in `src/matlab/main/+pm/+vis/Subplot.m` (lines 10 and 289) preventing MATLAB from recognizing `classdef`, verified locally that changing `!>` to `%>` fixed the issue, and filed Issue #52 (`https://github.com/cdslaborg/paramonte/issues/52`).

**2025-10-14**

Committed the `Subplot.m` fix (`a6d7c76`) and opened PR #53 (`https://github.com/cdslaborg/paramonte/pull/53`, "Update Subplot.m") with a clean +21/-21 single-file diff.

**2025-10-15**

Maintainer `@shahmoradi` merged PR #53 into `main` (`2025-10-15T21:38:20Z`). Later that same day, after encountering a property typo (`self._weblinks` instead of `self.weblinks`) in `getppm.m` and Xcode C MEX build issues on macOS, I opened PR #54 (`https://github.com/cdslaborg/paramonte/pull/54`) bundling `getppm.m`, `CMakeLists.txt` C++ enforcement, and a new `pm_sampling.cpp` MEX implementation.

**2025-10-17**

Maintainer `@shahmoradi` extracted the `getppm.m` fix from PR #54 into PR #55 (`https://github.com/cdslaborg/paramonte/pull/55`) and merged it into `main`, then commented on PR #54 explaining that the new C++ MEX file ran ~35× slower than the original C version and needed optimization and cross-platform testing. I merged `main` back into my fork branch (`2d2c7cfa`, `dfb0a673`) so PR #54's diff isolated only the remaining `CMakeLists.txt` and `pm_sampling.cpp` changes.

**Toolkit**

- Tool: Manual git workflow and GitHub web interface
- What it produced: Merged PR #53 (`Subplot.m`), merged upstream PR #55 (`getppm.m`), and clean branch history on follow-up PR #54
- Difficulties: Bundling multiple fixes into PR #54 delayed review of the larger C++ MEX change and required the maintainer to split out `getppm.m` manually
- What changed: Kept PR #53 strictly atomic, and synced `main` into PR #54 after PR #53 and PR #55 merged

---

## Pull Request

**PR Link:** https://github.com/cdslaborg/paramonte/pull/53 (follow-up work in https://github.com/cdslaborg/paramonte/pull/54 and https://github.com/cdslaborg/paramonte/pull/55)

**PR Description:** PR #53 ("Update Subplot.m", commit `a6d7c76`) updated `src/matlab/main/+pm/+vis/Subplot.m` (+21/-21 lines) to replace Fortran-style `!>` Doxygen comment prefixes on lines 10–19 and 289–298 with MATLAB-compatible `%>` comment markers so MATLAB recognizes `classdef Subplot < pm.vis.axes.Axes` and executes `readChain` and `readSample` visualizations properly:

```diff
--- a/src/matlab/main/+pm/+vis/Subplot.m
+++ b/src/matlab/main/+pm/+vis/Subplot.m
@@ -7,16 +7,16 @@
 %>  this class primarily serves as the superclass for the
 %>  visualization subclasses accessible to the end users.<br>
 %>
-!>  \note
-!>  In case of the following visualization classes,<br>
-!>  <ol>
-!>      <li>    [paramonte.vis.SubplotContour](@ref SubplotContour),
-!>      <li>    [paramonte.vis.SubplotContourf](@ref SubplotContourf),
-!>      <li>    [paramonte.vis.SubplotContour3](@ref SubplotContour3),
-!>  </ol>
-!>  the density of the input data is first computed and smoothed
-!>  by a Gaussian kernel density estimator and then passed to the
-!>  MATLAB ``contour``, ``contourf``, ``contour3`` intrinsics.<br>
+%>  \note
+%>  In case of the following visualization classes,<br>
+%>  <ol>
+%>      <li>    [paramonte.vis.SubplotContour](@ref SubplotContour),
+%>      <li>    [paramonte.vis.SubplotContourf](@ref SubplotContourf),
+%>      <li>    [paramonte.vis.SubplotContour3](@ref SubplotContour3),
+%>  </ol>
+%>  the density of the input data is first computed and smoothed
+%>  by a Gaussian kernel density estimator and then passed to the
+%>  MATLAB ``contour``, ``contourf``, ``contour3`` intrinsics.<br>
```

**Maintainer Feedback:**

- **2025-10-13:** Maintainer `@shahmoradi` assigned Issue #52 to himself for triage.
- **2025-10-15:** Maintainer `@shahmoradi` merged PR #53 directly into `main` without requesting changes.
- **2025-10-17:** On follow-up PR #54, `@shahmoradi` extracted and merged the `getppm.m` fix via PR #55 and commented: *"Thanks Nabin. I address part of your pull request (getppm) in a separate pull: Update getppm.m #55. For the C++ mex file, it appears to be 35 times slower than the original C file. Let's identify the cause of slow down, fix it, ensure its cross-platform portability and make another pull request."*


**Status:** Merged (`PR #53` merged into `main` on 2025-10-15; `getppm.m` fix merged via `PR #55` on 2025-10-17; follow-up `PR #54` remains open awaiting C++ MEX performance optimization).

**Toolkit**

- Tool: Manual GitHub PR review and branch sync (did not use `pr-monitor`)
- What it produced: Two merged upstream fixes (`PR #53` and `PR #55`) and actionable performance feedback on `PR #54`
- Difficulties: None on PR #53; PR #54 remains open due to the 35× runtime overhead in `pm_sampling.cpp`
- What changed: Synced `main` into PR #54 to remove already-merged files from the diff

---

## Learnings and Reflections
**Technical Skills Gained**

- Learned how MATLAB's class parser treats `!` as a shell-escape operator rather than a comment delimiter, and how Doxygen's `%>` syntax preserves documentation compatibility in `.m` class files.
- Gained practical experience building a multi-language scientific computing library (MATLAB, C/C++, CMake) from source on Apple Silicon (macOS 26 Tahoe, MATLAB R2025b) and debugging cross-language MEX interfaces.

**Challenges Overcome**

- Diagnosing why `readChain` and `readSample` failed during visualization when the error surfaced inside `pm.vis.Subplotheatmap`, tracing it back to comment syntax in the `Subplot` superclass.
- Navigating build and post-sampling friction on a new macOS release by compiling ParaMonte from its source release and isolating defects across MATLAB and C/C++ build layers.

**What I'd Do Differently Next Time**

- **Never bundle unrelated fixes into a single pull request.** PR #53 succeeded and merged within a day because it fixed one clear bug (`Subplot.m`) with a minimal diff. In PR #54, I combined a one-line `getppm.m` fix with CMake build changes and a 209-line C++ MEX rewrite, which forced the maintainer to extract `getppm.m` into PR #55 manually. Next time, I will open a separate, atomic pull request for each independent issue.
- **Benchmark performance-critical rewrites before submitting.** Replacing a C MEX interface with a C++ MEX wrapper introduced per-evaluation allocation and `feval` overhead that made `pm_sampling.cpp` 35× slower; timing the C vs. C++ MEX execution locally before opening PR #54 would have caught that regression immediately.

---

## Resources Used

- ParaMonte Issue #52 (bug report and root-cause notes): https://github.com/cdslaborg/paramonte/issues/52
- ParaMonte Pull Request #53 (merged `Subplot.m` fix): https://github.com/cdslaborg/paramonte/pull/53
- ParaMonte Pull Request #54 (follow-up Xcode/MEX PR and maintainer performance feedback): https://github.com/cdslaborg/paramonte/pull/54
- ParaMonte Pull Request #55 (merged `getppm.m` fix): https://github.com/cdslaborg/paramonte/pull/55
- ParaMonte `Subplot.m` source file in `cdslaborg/paramonte`: https://github.com/cdslaborg/paramonte/blob/main/src/matlab/main/%2Bpm/%2Bvis/Subplot.m
