# Improve CSS Snapshot Readability in Test Utilities

## Description

The shared test utilities that generate CSS output for snapshot comparisons currently trim the result before returning it. This causes snapshot values to start immediately with CSS content — for example, a snapshot might begin with `.foo {` on the same line as the opening quote. When a CSS block changes at the beginning, this format makes the diff harder to read.

Additionally, when the output is empty, the utilities return the raw (whitespace-trimmed) value, which makes it slightly ambiguous in snapshot format.

## Expected Behavior

- All CSS generation helpers in the shared test utility module should pass their output through a consistent formatter before returning.
- The formatter should produce a string that begins with a newline and ends with a newline when the CSS is non-empty, so snapshot comparisons have a clear boundary at both ends.
- When the CSS output is empty or contains only whitespace, the formatter should return an exact empty string so that empty-output checks can use a simple equality assertion.
- The formatter should be exported from the test utility module so individual tests can use it directly when they process CSS output outside of the shared helpers.

## Why This Matters

Consistent formatting of snapshot values makes it easier for developers to read and review test output. A leading newline ensures the first line of CSS appears on its own line in the snapshot, making diffs cleaner and reducing confusion when the start of the CSS block changes. Explicit empty-string output for empty CSS makes it easy to distinguish "no output" from "some whitespace-only output" in test assertions.
