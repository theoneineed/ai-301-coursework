# Fixing Visualization and Post-Sampling Bugs in ParaMonte on macOS

## Overview

ParaMonte (`cdslaborg/paramonte`) is an open-source parallel Monte Carlo and machine learning library with a MATLAB interface. While running ParaMonte on Apple Silicon (macOS 26 Tahoe), post-sampling read functions (`readChain` and `readSample`) crashed because MATLAB misparsed Doxygen comment markers in `Subplot.m`, a typo in `getppm.m` broke post-sampling output, and Xcode build changes blocked C MEX compilation. I filed [Issue #52](https://github.com/cdslaborg/paramonte/issues/52), shipped the `Subplot.m` fix in merged [PR #53](https://github.com/cdslaborg/paramonte/pull/53), and bundled the remaining fixes (`getppm.m`, `CMakeLists.txt`, and a new C++ MEX file `pm_sampling.cpp`) into [PR #54](https://github.com/cdslaborg/paramonte/pull/54). Maintainer `@shahmoradi` extracted and merged the `getppm.m` fix separately via [PR #55](https://github.com/cdslaborg/paramonte/pull/55), while PR #54 remains open today because the C++ MEX rewrite ran 35 times slower than the original C implementation.

## What happened

### 1. Diagnosing and fixing the `Subplot.m` class-parsing failure (Oct 12–15, 2025)

On October 12, 2025, I opened [Issue #52](https://github.com/cdslaborg/paramonte/issues/52) after `readChain` and `readSample` failed during visualization on an Apple Silicon M1 Pro running macOS 26 Tahoe. ParaMonte successfully read the output chain files from disk, but when `pm.vis.Subplotheatmap` called `pm.vis.Subplot`, MATLAB failed to recognize `Subplot` as a valid class.

Inspecting `src/matlab/main/+pm/+vis/Subplot.m`, I found Fortran-style `!>` Doxygen comment blocks on lines 10 and 289 preceding `classdef Subplot < pm.vis.axes.Axes`. Because MATLAB treats `!` as a shell-escape operator rather than a comment delimiter, it did not see `classdef` as the first executable statement. On October 14, I opened [PR #53](https://github.com/cdslaborg/paramonte/pull/53) to replace `!>` with MATLAB's `%>` Doxygen-compatible comment prefix:

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

Maintainer @shahmoradi merged PR #53 into main on October 15, 2025, restoring post-sampling visualization.

2. Bundling post-sampling and Xcode MEX fixes into PR #54 (Oct 15, 2025)

While getting the full sampling-to-visualization pipeline working locally on macOS, I ran into two more blockers: a property typo (self._weblinks instead of self.weblinks) in src/matlab/main/+pm/+sampling/@Paradram/getppm.m that crashed post-sampling reporting, and Xcode C MEX compilation errors in src/matlab/xrc/. Rather than opening separate pull requests for each fix, I bundled them together in PR #54 (commit f08401e7), updating CMakeLists.txt to enforce CXX and adding a 209-line C++ MEX replacement (pm_sampling.cpp):

```diff
--- a/src/matlab/xrc/CMakeLists.txt
+++ b/src/matlab/xrc/CMakeLists.txt
@@ -60,9 +61,17 @@ add_custom_target(matlabmex)
 unset(xtargets)
 foreach(xtarget ${XRC_FILES})
     set(xlibname "${xtarget}")
-    set(xrcpath "${CMAKE_CURRENT_SOURCE_DIR}/${xtarget}.c")
+    set(xrcpath "${CMAKE_CURRENT_SOURCE_DIR}/${xtarget}.cpp")
+
+    # EXPLICITLY SET THE LANGUAGE TO C++
+    set_source_files_properties("${xrcpath}" PROPERTIES LANGUAGE CXX)
+
     #set(xlibname "${xtarget}_${build}_${mem}_${parname}_${checking}")
     matlab_add_mex(NAME "${xtarget}" SHARED SRC "${xrcpath}" OUTPUT_NAME "${xlibname}" LINK_TO "${libname}" R2018a)
+
+    # Ensure the target language is C++
+    set_target_properties("${xtarget}" PROPERTIES LINKER_LANGUAGE CXX)
```

3. Maintainer split, PR iteration, and what landed (Oct 17, 2025)
Because PR #54 mixed a trivial one-line MATLAB bug fix with a major C++ MEX rewrite, @shahmoradi extracted the getppm.m fix into PR #55 and merged it on October 17, 2025:

```diff
--- a/src/matlab/main/+pm/+sampling/@Paradram/getppm.m
+++ b/src/matlab/main/+pm/+sampling/@Paradram/getppm.m
@@ -17,7 +17,7 @@
 %>  \author
 %>  \AmirShahmoradi, September 1, 2012, 12:00 AM, National Institute for Fusion Studies, The University of Texas at Austin<br>
 function ppm = getppm(self)
-    baselink = self._weblinks.docs.generic.usage.sampling.paradram.quickstart.url
+    baselink = self.weblinks.docs.generic.usage.sampling.paradram.quickstart.url
     ppm = getppm@pm.sampling.Sampler(self) ...
```

Four minutes later, @shahmoradi left this review comment on PR #54 explaining why the C++ MEX portion could not be merged yet:
Thanks Nabin. I address part of your pull request (getppm) in a separate pull: Update getppm.m #55
For the C++ mex file, it appears to be 35 times slower than the original C file.
Let's identify the cause of slow down, fix it, ensure its cross-platform portability and make another pull request.

In response to PR #53 and PR #55 landing upstream, I updated my PR #54 branch by merging main back into it (commits 2d2c7cfa and dfb0a673), removing the already-merged Subplot.m and getppm.m changes from the diff so PR #54 isolated only the remaining CMakeLists.txt and pm_sampling.cpp work. As a direct result of this investigation, two fixes landed in cdslaborg/paramonte main (PR #53 for Subplot.m and PR #55 for getppm.m), while PR #54 remains open awaiting performance optimization.

## Limitations and what's next

My work has two clear limitations. First, pm_sampling.cpp suffers a roughly 35× runtime slowdown compared to the original C MEX file. Inside its per-evaluation callbacks (getLogFuncOMP and getLogFuncScalar), every likelihood call instantiates a new matlab::data::ArrayFactory, allocates a new TypedArray<double>, copies state buffers, and invokes MATLABEngine::feval—introducing heavy per-iteration allocation and dispatch overhead that was absent in the pointer-based C MEX API. Second, PR #54 included no automated timing benchmark or cross-platform build verification, and bundling unrelated fixes into one pull request slowed down review.

Next, I plan to:

Profile pm_sampling.cpp to eliminate per-call ArrayFactory allocations and feval indirection—or verify whether updated CMake compiler flags let the original C MEX file build cleanly under modern Xcode without a C++ rewrite.
