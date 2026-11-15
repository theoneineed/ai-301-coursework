# Contribution: Defect: issue with reading files due to a bug in pm.vis.Subplot file

**Contribution Number:** 1
**Student:** Nabin Chapagain
**GitHub Username:** theoneineed
**Issue:** https://github.com/cdslaborg/paramonte/issues/52
**Pull Request:** https://github.com/cdslaborg/paramonte/pull/53


---

## Toolkit

**Unit 5: Grading my ParaMonte issue with issue-scout and uncovering an assignee blind spot**

I updated my `issue-scout` fit profile (`scope.md`) to target MATLAB, C/C++, CMake, and scientific simulation software, then ran `issue-scout: grade https://github.com/cdslaborg/paramonte/issues/52` on the bug I discovered in ParaMonte. Four of the five checks passed (`maintainer-active`, `human-authored`, `valid-policy`, and `manageable-scope`), but `unclaimed` failed with a `reject` verdict because `assignees: ["theoneineed"]` was non-empty and my own PR #53 had already merged. This revealed a concrete flaw in `rubric.md`: the `unclaimed` rule treats any assignee as a competing claim rather than checking whether the assignee matches my own GitHub handle (`theoneineed`).

**Unit 6: Manual root-cause analysis and upstream communication without voice-guard**

I did not install or use `voice-guard` or a repo-memory file during this contribution. I manually inspected the ParaMonte MATLAB source files, traced the class-parsing failure in `Subplot.m` to Fortran-style `!>` Doxygen comment markers preceding `classdef`, and wrote the issue report and pull request descriptions myself in plain technical prose.

**Unit 7: Manual local verification and PR submission without ship-gate**

I did not install or run `ship-gate` or create a `ship-manifest.md` file. I verified my `Subplot.m` fix locally in MATLAB R2025b by re-running `pm.runSampler()` and checking that the output figures rendered properly, then submitted PR #53 manually; when I later bundled additional fixes into a second pull request (PR #54), skipping a pre-submission scope check led me to combine unrelated changes in one PR.

**Unit 8: Manual maintainer follow-up and branch maintenance without pr-monitor**

I did not install or use `pr-monitor` or an automated response playbook. I read maintainer `@shahmoradi`'s feedback directly on GitHub after PR #53 merged and PR #55 split out my `getppm.m` fix, and I manually merged `main` back into my fork branch so the remaining open PR reflected only the unmerged C++ MEX changes.

---

## Why I Chose This Issue

**Claim Comment:** None posted on GitHub — I discovered and filed Issue #52 myself at https://github.com/cdslaborg/paramonte/issues/52 rather than claiming an existing issue from the AI301 Issue Selection site.

I discovered this bug organically while running ParaMonte (`cdslaborg/paramonte`) in MATLAB R2025b on macOS for my own Monte Carlo simulation and post-processing workflow. When I ran `pm.runSampler()`, the simulation completed but crashed during post-sampling visualization inside `pm.vis.Subplot`. When I evaluated the issue against my `issue-scout` fit profile (`scope.md`, configured for MATLAB, C/C++, CMake, and scientific computing libraries) using `issue-scout: grade https://github.com/cdslaborg/paramonte/issues/52`, the scout passed `maintainer-active`, `human-authored`, `valid-policy`, and `manageable-scope`, but returned `verdict: "reject"` on `unclaimed` (`assignees: ["theoneineed"]` was non-empty after maintainer `@shahmoradi` assigned the issue to me on October 13, 2025, and my own PR #53 had already merged on October 15, 2025). Proceeding with this issue was still the right choice because it matched my domain profile and solved a real blocker in software I actively use.

Running `issue-scout` on a real self-filed issue exposed a clear limitation in `rubric.md`: the `unclaimed` check inspects `assignees` blindly without comparing the assignee username against the student's own GitHub handle (`theoneineed`), causing it to flag a student's own self-assigned issue as "already claimed." Updating `rubric.md` to exempt the contributor's own username in `assignees` would prevent that false negative.

**Toolkit**

- Tool: `issue-scout` (`scope.md` fit profile and `rubric.md` via `issue-scout: grade https://github.com/cdslaborg/paramonte/issues/52`)
- What it produced: Passed 4/5 checks (`maintainer-active`, `human-authored`, `valid-policy`, `manageable-scope`) and returned `verdict: "reject"` solely on `unclaimed` because `assignees: ["theoneineed"]` was non-empty and PR #53 was already merged
- Difficulties: `rubric.md` failed to distinguish an external claim from the issue being assigned to me (`theoneineed`), producing a false rejection
- What changed: Identified the missing self-assignee exemption in `rubric.md` (`assignees` check) and kept PR #53 tightly scoped to `Subplot.m`

---

## Understanding the Issue

**Problem Description**

ParaMonte's MATLAB sampler and post-sampling read utilities (`pm.runSampler()`, `readChain`, and `readSample`) fail during the visualization phase because MATLAB cannot recognize `pm.vis.Subplot` as a valid class definition.

**Expected Behavior**

Running `pm.runSampler()` (or calling `readChain` / `readSample` on completed output files) should read the generated chain/sample data from disk and initialize `pm.vis.Subplot` cleanly so downstream visualization classes (such as `pm.vis.Subplotheatmap`) can render figures without errors.

**Current Behavior**

ParaMonte runs the sampler and reads the output file from disk, but as soon as the post-sampling visualization step invokes `pm.vis.Subplotheatmap`—which in turn calls `pm.vis.Subplot`—MATLAB throws an error stating that it cannot find or recognize the `pm.vis.Subplot` function/class.

**Affected Components**

- `src/matlab/main/+pm/+vis/Subplot.m` (superclass for ParaMonte MATLAB visualization objects, specifically lines `10–19` in the header comment block before `classdef` on line `192`, and lines `289–298` inside the class body)
- `src/matlab/main/+pm/+vis/Subplotheatmap.m`, `pm.runSampler()`, and post-sampling read functions (`readChain`, `readSample`) that depend on `pm.vis.Subplot`

**Toolkit**

- Tool: Manual code inspection in MATLAB R2025b (no automated analysis skill used)
- What it produced: Traced the failure from `pm.runSampler()` -> `pm.vis.Subplotheatmap` -> `src/matlab/main/+pm/+vis/Subplot.m`, pinpointing Fortran-style `!>` Doxygen markers preceding `classdef`
- Difficulties: The runtime error surfaced inside `pm.vis.Subplotheatmap` rather than naming the syntax error inside `Subplot.m`, requiring direct inspection of `Subplot.m`
- What changed: Nothing in course tools; documented the exact file path and line numbers (`line 10` and `line 289`) directly in Issue #52

---

## Reproduction Process

**Environment Setup**

- **Hardware & OS:** Apple MacBook (Apple Silicon M1 Pro), macOS 26 Tahoe
- **Runtime:** MATLAB R2025b
- **ParaMonte Version & Build:** ParaMonte 2 C, C++, Fortran prerelease (`Version 2024.04.29 -- C/C++/Fortran prerelease`, library target `libparamonte_matlab_darwin_arm64`), downloaded from GitHub and built locally from source using its build script/binary rather than using only the pre-built app bundle.
- **Setup Friction & Resolution:** During local compilation on macOS 26 Tahoe, Xcode blocked building the original C MEX file (`src/matlab/xrc/pm_sampling.c`). I resolved that build friction locally by rewriting the MEX wrapper as a C++ file (`src/matlab/xrc/pm_sampling.cpp`) and enforcing `CXX` language compilation in `src/matlab/xrc/CMakeLists.txt` (later submitted in PR #54, where maintainer `@shahmoradi` noted that the C++ MEX implementation ran ~35× slower than the original C version; optimizing that C++ MEX overhead remains on my to-do list).

**Steps to Reproduce**

1. Build and load `libparamonte_matlab_darwin_arm64` (ParaMonte 2 prerelease `Version 2024.04.29`) in MATLAB R2025b on macOS 26 Tahoe.
2. Execute the MATLAB sampler via `pm.runSampler()` on a simulation run (which runs sampling and automatically invokes post-sampling chain reading and visualization; `readChain` alone only reads an already-generated chain file).
3. Observe the failure during the post-sampling visualization step when `pm.vis.Subplotheatmap` attempts to call `pm.vis.Subplot`: MATLAB fails to locate/parse `pm.vis.Subplot` because Fortran-style `!>` Doxygen comment lines sit inside the header comment block (`lines 10–19`) preceding `classdef Subplot < pm.vis.axes.Axes` on `line 192`.

**Reproduction Evidence**

- **Commit showing reproduction:** Reproduced locally in MATLAB R2025b and documented in https://github.com/cdslaborg/paramonte/issues/52 (fixed in commit https://github.com/cdslaborg/paramonte/commit/a6d7c76 via https://github.com/cdslaborg/paramonte/pull/53)
- **Screenshots/logs:** Screenshot of the MATLAB error attached directly to Issue #52 (https://github.com/user-attachments/assets/27a68803-cb36-49eb-ab66-92636119ccde), accompanied by the reported symptom: (content of screenshot: Error using pm.vis.SubplotHeatmap The specified superclass 'pm.vis.Subplot' contains a parse error, cannot be found on Matlab's search path, or is shadowed by another file with the same name. Error in pm.vis.PlotHeatmap (line 84))
- **My findings:** In `src/matlab/main/+pm/+vis/Subplot.m`, lines `1–191` form the top-of-file documentation header before `classdef Subplot < pm.vis.axes.Axes` begins on `line 192`. Because MATLAB treats `%` as a comment delimiter and `!` as a shell-escape operator, the Fortran-style `!>` Doxygen `\note` blocks at `lines 10–19` (before `classdef`) and `lines 289–298` (inside the class body) prevented MATLAB from recognizing `classdef` as the first executable statement.

**Toolkit**

- Tool: Manual reproduction in MATLAB R2025b (did not use `repro-check`)
- What it produced: Confirmed reproducible crash during `pm.runSampler()` post-sampling visualization and isolated the `!>` syntax conflict in `Subplot.m`
- Difficulties: Xcode C MEX compilation failed during initial source setup on macOS 26 Tahoe, requiring a local C++ MEX workaround (`pm_sampling.cpp`) before running MATLAB sampling end-to-end
- What changed: Nothing in course tools

---
## Solution Approach

**Analysis**

`src/matlab/main/+pm/+vis/Subplot.m` mixed MATLAB comment markers (`%>`) with Fortran-style Doxygen markers (`!>`). While Doxygen recognizes both prefixes, MATLAB requires `classdef` to be the first executable statement in a class file and treats `!` as a shell escape rather than a comment. Converting the `!>` blocks to `%>` preserves Doxygen documentation generation while making the file valid MATLAB syntax.

**Proposed Solution**

Replace every `!>` Doxygen comment prefix in `src/matlab/main/+pm/+vis/Subplot.m` (lines `10–19` and `289–298`) with `%>`. I deliberately left the documentation wording, Doxygen tags (`\note`, `@ref`), and all class methods and properties untouched so the fix remained minimal, safe, and easy to review.

**Implementation Plan**

1. Open `src/matlab/main/+pm/+vis/Subplot.m` and replace `!>` with `%>` in the header note block (`lines 10–19`, before `classdef` on `line 192`).
2. Replace `!>` with `%>` in the second note block (`lines 289–298`, inside the class body) in the same file, and search `src/matlab/` to verify no other `.m` files contain `!>` markers.
3. Re-run `pm.runSampler()` in MATLAB R2025b to verify that `pm.vis.Subplot` loads cleanly and post-sampling figures render.

*(Deviation note: PR #53 followed this exact 3-step plan and merged cleanly. Afterward, while testing additional parts of the macOS build, I opened a separate pull request—PR #54—where I deviated from single-issue scoping by bundling a `getppm.m` typo fix with CMake C++ flags and the `pm_sampling.cpp` MEX file.)*

**Toolkit**

- Tool: Manual source analysis and planning (did not use `plan-check`)
- What it produced: A targeted 2-block comment-prefix replacement plan in `Subplot.m`
- Difficulties: None met for PR #53
- What changed: Nothing in course tools

---

## Testing Strategy

Because `cdslaborg/paramonte` has no automated MATLAB CI test suite configured on pull requests, I verified the fix using codebase-wide pattern checks and before-and-after sampler execution in MATLAB R2025b on macOS:

1. **Codebase-wide `!>` search:** Searched the rest of `src/matlab/` for Fortran-style `!>` Doxygen comment markers and confirmed that `Subplot.m` was the only MATLAB file affected by that pattern.
2. **Before fix:** Running `pm.runSampler()` in MATLAB R2025b failed during post-sampling visualization when `pm.vis.Subplotheatmap` called `pm.vis.Subplot`, throwing an undefined class/function error before any figures could render.
3. **After fix:** After changing `!>` to `%>` on lines `10–19` and `289–298` of `Subplot.m`, MATLAB parsed `classdef Subplot < pm.vis.axes.Axes` cleanly, and re-running `pm.runSampler()` completed end-to-end with all output figures generated and available without errors.
4. **Diff inspection:** Checked `git diff` prior to opening PR #53 (`+21/-21` lines in a single file) to confirm only the intended comment markers were modified.

**Toolkit**

- Tool: Manual MATLAB R2025b execution, codebase search for `!>`, and `git diff` review (did not use `pr-precheck`)
- What it produced: Before/after confirmation that `pm.runSampler()` completes and generates post-sampling figures cleanly, plus confirmation that no other `.m` files contained `!>` markers
- Difficulties: No automated MATLAB test runner was configured upstream, requiring manual end-to-end sampler verification
- What changed: Nothing in course tools

---
## Implementation Notes

**2025-10-12**

While running ParaMonte (`Version 2024.04.29`, `libparamonte_matlab_darwin_arm64`) in MATLAB R2025b on my MacBook (Apple Silicon M1 Pro, macOS 26 Tahoe), `pm.runSampler()` crashed during post-sampling plotting. I traced the error to `!>` Doxygen comment blocks in `src/matlab/main/+pm/+vis/Subplot.m` (`lines 10–19` before `classdef` on `line 192`, and `lines 289–298`) preventing MATLAB from recognizing `classdef`, confirmed no other files in `src/matlab/` used `!>`, verified locally that changing `!>` to `%>` restored figure generation, and filed Issue #52 (https://github.com/cdslaborg/paramonte/issues/52).

**2025-10-14**

Committed the `Subplot.m` fix (https://github.com/cdslaborg/paramonte/commit/a6d7c76) and opened PR #53 (https://github.com/cdslaborg/paramonte/pull/53, "Update Subplot.m") with a single-file diff.

**2025-10-15**

Maintainer `@shahmoradi` merged PR #53 into `main` (`2025-10-15T21:38:20Z`). Later that same day, after encountering a property typo (`self._weblinks` instead of `self.weblinks`) in `getppm.m` and Xcode C MEX build issues on macOS, I opened PR #54 (https://github.com/cdslaborg/paramonte/pull/54) bundling `getppm.m`, `CMakeLists.txt` C++ enforcement, and `pm_sampling.cpp`.

**2025-10-17**

Maintainer `@shahmoradi` extracted the `getppm.m` fix from PR #54 into PR #55 (https://github.com/cdslaborg/paramonte/pull/55) and merged it into `main`, then commented on PR #54 explaining that `pm_sampling.cpp` ran ~35× slower than the original C MEX file and needed optimization and cross-platform testing. I merged `main` back into my fork branch (`2d2c7cfa`, `dfb0a673`) so PR #54's diff isolated only the remaining `CMakeLists.txt` and `pm_sampling.cpp` changes.

**Toolkit**

- Tool: Manual git workflow and GitHub web interface
- What it produced: Merged PR #53 (`Subplot.m`), merged upstream PR #55 (`getppm.m`), and clean branch history on follow-up PR #54
- Difficulties: Bundling multiple fixes into PR #54 delayed review of the larger C++ MEX change and required the maintainer to split out `getppm.m` manually
- What changed: Kept PR #53 strictly atomic, and synced `main` into PR #54 after PR #53 and PR #55 merged

---

## Pull Request
**PR Link:** https://github.com/cdslaborg/paramonte/pull/53

**PR Description:** PR #53 ("Update Subplot.m", commit https://github.com/cdslaborg/paramonte/commit/a6d7c76, files view: https://github.com/cdslaborg/paramonte/pull/53/files) updated `src/matlab/main/+pm/+vis/Subplot.m` (`+21/-21` lines) to replace Fortran-style `!>` Doxygen comment prefixes on lines `10–19` and `289–298` with MATLAB-compatible `%>` comment markers so MATLAB recognizes `classdef Subplot < pm.vis.axes.Axes` (line `192`) and executes `pm.runSampler()` visualizations properly:

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

- Diagnosing why `pm.runSampler()` failed during post-sampling visualization when the error surfaced inside `pm.vis.Subplotheatmap`, tracing it back to comment syntax in the `Subplot` superclass.
- Navigating build and post-sampling friction on a new macOS release by compiling ParaMonte (`Version 2024.04.29`, `libparamonte_matlab_darwin_arm64`) from its source release and isolating defects across MATLAB and C/C++ build layers.

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
