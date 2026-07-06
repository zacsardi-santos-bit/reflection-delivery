## Description

Bazel's test infrastructure incorrectly determines the operating system of the platform that will execute tests when execution groups are involved. When a test rule defines a custom execution group with specific OS platform constraints, or when a test redirects execution to an alternative exec group, the test runner uses the wrong platform to figure out what OS the tests will run on.

This means the test runner may generate the wrong type of test wrapper scripts (e.g., Windows batch files when the tests should actually run on Linux or macOS), use the wrong shell toolchain path, and make other OS-specific decisions based on incorrect information.

## Expected Behavior

- When a test rule defines a test execution group that requires a specific OS (such as macOS), the test's execution settings should report that OS as the execution OS — even if the test target itself has different top-level platform constraints.
- When a test uses an execution info provider to redirect testing to an alternative execution group, the OS should be read from that alternative group's execution platform.
- The OS-to-constraint mapping used across the codebase should recognize the canonical macOS constraint alias (in addition to the legacy one), so that platforms declaring macOS compatibility using either name are correctly identified.

## Related Refactoring

Alongside the behavioral fix, the methods and fields used to determine OS from platform constraints need to be updated. The existing OS-constraint lookup should be replaced with a cleaner API that accepts a full platform object rather than just a constraint collection, and falls back to the host OS only when the platform specifies no OS constraint at all (rather than defaulting to the host OS for unrecognized constraints).

## Why This Matters

Developers who use execution groups to route test workloads to specific OS environments (in heterogeneous or cross-platform build setups) need the test runner to accurately reflect the target execution environment so that the generated test scripts and shell configurations are correct for where the tests will actually run.
