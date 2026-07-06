## Description

The error classification system in Storybook's testing infrastructure doesn't recognize many common error patterns that occur when components fail to render. Additionally, there is no shared set of utility functions for converting raw test results (which can arrive in different shapes from different sources) into a consistent structured format suitable for analysis.

## Problems to Fix

**Error classification gaps:**
- Infinite render loop warnings are not categorized correctly
- Errors about updating component state causing infinite loops are not recognized
- Errors from custom hooks called outside function components are missed
- Portal container errors that specifically mention an invalid DOM target are not caught
- Missing context provider errors indicating the context is absent, unavailable, or the provider was not found are not matched
- Many common component render failures — undefined references, invalid element types, non-function components, invalid React children, and stack overflows — are not classified

**Missing shared utilities:**
- There is no utility to normalize raw test results into a consistent normalized story result format
- There is no utility to extract a clean, human-readable error message from test framework error objects (which may include Storybook's debug banner prepended to the message)
- There is no utility to detect whether a story rendered an empty element based on render-analysis reports
- There is no utility to compute aggregate statistics (pass rate, empty render count, categorized errors) from a list of story test results

## Expected Behavior

- The error classifier correctly recognizes the additional error patterns listed above
- A conversion function handles results from both JSON reporter output and runtime test runners, normalizing them into a consistent format
- An error message extraction function strips the Storybook debug banner (whether ANSI-colored or plain text) and returns only the first line of the actual error
- A test results analysis function produces a summary with total, passed, passed-but-empty-render counts, success rates rounded to two decimal places, and categorized errors

## Why This Matters

These utilities will be used in multiple parts of the codebase and need to behave consistently. Getting the error classification right is important for producing accurate telemetry and useful developer feedback about why stories fail.
