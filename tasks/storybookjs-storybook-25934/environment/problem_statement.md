## Description

Storybook needs a way to detect and report environment-level compatibility issues before an upgrade or initialization proceeds. Right now, if a user's environment is incompatible (e.g., wrong runtime version), the operation fails mid-way without clearly communicating what went wrong. There's no pre-flight check system to identify blockers upfront and surface them in a structured way.

## Expected Behavior

- A blocking check system should run a series of compatibility checks against the current environment.
- When no issues are detected (or when no checks are provided), the system should return a clean result and optionally report that no blockers were found.
- When one or more checks fail, the system should:
  - Return the identifier of the first failing check.
  - Write a structured log file listing each failing check by name and its diagnostic output.
  - Multiple failures should be separated clearly in the log file.
  - Notify the user with a prominent warning message.
- Individual checks should be definable with an identifier, a check function, a user-facing message, and a log output string.

## Why This Matters

Users upgrading Storybook or setting it up for the first time deserve to know immediately and clearly if their environment has compatibility issues. A structured pre-flight blocking check system prevents wasted time on operations that are doomed to fail and gives users actionable diagnostic output.
