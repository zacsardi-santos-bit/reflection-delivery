## Description

The rustup test suite's interactive installer tests currently use a verbose, two-step assertion style: commands are run, the output is stored in a variable, and then separate manual assertions are made against individual fields of that output. This approach requires deprecated helper macros for host-triple string interpolation, produces noisy debug print statements in many tests, and makes tests harder to read at a glance.

## Expected Behavior

- Tests should be able to chain assertions directly on the result of running a command — checking for success, failure, and expected stdout/stderr content in a single expression.
- Output matching should support pattern-based wildcards (e.g., matching any content before or after a key phrase), so tests don't need to match exact output.
- Host-triple values in expected output should be handled via a standard substitution mechanism instead of a deprecated macro.
- It should be possible to assert that a certain string does NOT appear in the output, as a chainable operation.
- Running a command with custom environment variables should produce the same chainable assertion result.

## Why This Matters

Removing boilerplate from tests makes them easier to read and maintain. Using a pattern-based output matcher instead of substring checks or exact string matching makes tests more robust to minor output formatting changes. Eliminating the deprecated macro reduces compiler warnings and moves the codebase toward modern idioms.
