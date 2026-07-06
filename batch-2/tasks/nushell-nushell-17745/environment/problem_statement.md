## Description

Writing tests for nushell commands currently requires a verbose, error-prone pattern to verify that output contains an expected substring: you must call the container's own "contains" method inside a generic assertion. When the check fails, the output gives no context about what was actually in the container or what was expected, making debugging slow. Additionally, tests that invoke external programs (like shell commands) fail silently on some systems because the test environment does not inherit the host's executable search path.

## Expected Behavior

- There should be a dedicated assertion helper for containment checks that accepts the needle and haystack in a readable order and automatically produces an informative failure message showing both what was searched for and what the container actually held.
- The helper should work uniformly across strings, collections, and ranges without requiring callers to know the container's concrete type.
- It should be available automatically when tests import the standard test support prelude.
- The test runner should offer an option to inherit the host system's executable search path so that tests depending on external programs can find those executables reliably.

## Why This Matters

These are quality-of-life improvements for test authors. Clearer failure messages reduce debugging time, and consistent assertion patterns make tests easier to read and maintain. The path inheritance feature unblocks tests for commands that rely on spawning external processes, which previously had to work around the sanitized test environment.
