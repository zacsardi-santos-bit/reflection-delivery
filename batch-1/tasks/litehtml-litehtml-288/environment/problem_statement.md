## Description

The litehtml project should be built with strict compiler warnings enabled, treating all warnings as errors. Currently the build uses lenient compiler settings, which silently allows a large number of common C++ coding issues to remain in the codebase.

When strict warning settings are turned on, the entire test suite fails immediately because the project no longer compiles. The warnings that become errors include:

- Signed/unsigned integer comparison mismatches (e.g., comparing a loop counter against a container size method result)
- Partially initialized aggregate data structures (struct or array initializers that leave trailing fields unset)
- Unused named function parameters in method signatures
- Comparing the result of a string search operation against a signed literal instead of the proper unsigned sentinel value
- Checking string emptiness via equality comparison rather than using the dedicated method
- Using integer zero as a null pointer return value

In addition, some URL parsing test cases are incomplete: they specify only three of the five URL components in their expected-result data, leaving the query and fragment fields without explicit values. Under strict warning settings this also triggers a warning.

## Expected Behavior

- The build configuration should enable strict compiler warnings for all supported compilers
- All of the above warning categories must be resolved throughout the codebase and test infrastructure so the project compiles cleanly
- URL test cases must fully specify all five URL components (scheme, host, path, query string, and fragment) in their expected-result entries

## Why This Matters

Without strict warnings, latent bugs can hide undetected. Enabling strict warnings and resolving them ensures better code quality and makes the project more maintainable. Once the build is clean under strict settings, the full test suite can execute and verify correctness.
