## Description

Twister currently has no concept of which toolchain was used for a given test instance. The toolchain is implicitly determined by the environment at run time, but this information is never stored with the test instance, reflected in the build directory structure, included in log output, or carried through to saved test results. This creates a real problem when testing the same suite against multiple toolchains: build directories collide, instance lookup keys are ambiguous, and it's impossible to distinguish results from different toolchain runs in reports or saved plans.

## Expected Behavior

- Each test instance should record which toolchain it was built with.
- The build directory path should include the toolchain as a distinct directory level, preventing directory collisions when a test is run with multiple toolchains.
- Test instance keys used to look up results (including in quarantine files) should include the toolchain in the key path.
- Serialized test plans (saved to and loaded from disk) should include the toolchain information per instance.
- The cmake build invocation should pass the active toolchain to the build system.
- Progress log messages should display the active toolchain next to the timing information, so it's visible which toolchain was used for each result.
- Test YAML configuration files should support a new option to specify a list of toolchains, causing the test runner to automatically expand the test matrix to cover each platform and toolchain combination. This expansion should always apply, not only under a specific command-line flag.

## Why This Matters

Teams that need to validate firmware against multiple compilers (e.g., GCC versus LLVM versus a vendor toolchain) currently have to run Twister separately for each toolchain, manage output directories manually to avoid conflicts, and cross-reference results by hand. With explicit toolchain tracking built into the framework, this can be automated end-to-end: declare the desired toolchains in the test file, and get one clear test matrix with no directory conflicts and unambiguous result attribution.
