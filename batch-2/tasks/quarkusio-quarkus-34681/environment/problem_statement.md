## Description

When running a Quarkus test suite that contains tests belonging to different application contexts (each with its own classloader), the test class orderer does not account for classloader boundaries. The orderer only understands how to group tests by profile and test resources, so when the test framework has already assigned test classes to distinct classloaders, the orderer may intermix those groups — potentially triggering unnecessary application restarts or resource conflicts.

## Expected Behavior

- When test classes have been loaded by multiple distinct classloaders, the orderer should recognize this and group all tests from the same classloader together.
- Within each classloader group, any configured secondary ordering (such as alphabetical class name order or an explicit ordering annotation) should still be respected.
- When all test classes share the same classloader, the existing profile- and resource-based ordering should continue to work exactly as before.

## Why This Matters

Without this fix, tests that share a runtime environment can be interleaved with tests from a different runtime environment. This forces the framework to start and stop applications more times than necessary and can cause subtle resource conflicts. The fix ensures tests that belong together run consecutively, reducing both test execution time and the likelihood of environment-related failures.
