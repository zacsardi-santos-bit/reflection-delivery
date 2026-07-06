## Description

The project's CI pipeline runs end-to-end browser tests across multiple workflow runs, but there is currently no automated tooling to collect, analyze, and report on test flakiness over time. We need two new scripts:

1. A script that parses browser end-to-end test reports in JSON format from a single CI run and writes structured output files listing failed tests and tests that have been temporarily disabled pending a fix.
2. A script that aggregates those structured outputs across many recent CI runs, computes failure rates to identify the most problematic tests, and posts a formatted summary to a team channel with proper escaping of special characters in test names and error messages.

## Expected Behavior

- The extraction script reads a browser test results file, extracts failed tests (with error details) and temporarily-disabled tests (with their annotations), and writes them to separate output files. If no results file is found, it writes empty outputs indicating no data was available.
- Error messages longer than 300 characters must be truncated. Whitespace and newlines in error messages must be normalized.
- Test titles should be constructed from the file path and the full path of nested suite names joined appropriately.
- The analysis script aggregates failure data from multiple runs, computes a failure rate per test, and filters out tests below a minimum threshold. Results must be sorted by failure rate (highest first).
- The Slack-formatted message must include a fixed target channel identifier, a text fallback, and structured blocks. When no problematic tests are found, the message should clearly indicate this. All test names and error text must have special characters escaped for safe rendering.

## Why This Matters

Without this tooling, identifying persistently flaky tests requires manual inspection of many individual CI runs. This automation makes it easier to catch and address flakiness proactively, keeping the test suite reliable.
