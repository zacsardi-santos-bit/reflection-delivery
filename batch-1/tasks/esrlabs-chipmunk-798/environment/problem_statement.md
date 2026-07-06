## Description

The timestamp parsing system has several interrelated issues that need to be addressed:

1. **No way to validate format strings upfront**: There is currently no function to check whether a given timestamp format string is well-formed and contains all the required components (year, month, day, hour, minute). Users find out a format is invalid only when attempting to parse an actual log file, which makes it hard to surface errors early in the workflow.

2. **Seconds are required but shouldn't be**: The timestamp extractor fails on log lines that contain hours and minutes but no seconds field. Seconds should be treated as optional — if absent, the value should be assumed to be zero.

3. **Year fallback not propagated**: The file timespan and line scanning functions do not accept a fallback year, which means files containing log lines without a year component cannot be given a year hint at this level of the API.

4. **Incorrect integer types for file sizes**: File sizes obtained from filesystem metadata are being cast to a smaller type before being passed to indexing functions, which is unnecessary and could cause issues. These should use the native 64-bit size type throughout.

## Expected Behavior

- A new format validation function should accept a format string and return either a success result (with the compiled regex pattern) or a failure result (with a description of what is missing).
- Timestamp extraction should tolerate the absence of a seconds field, defaulting to 0.
- The file timespan and line scanning functions should accept an optional fallback year parameter.
- File size values should not be narrowed to a smaller integer type when passed through indexing pipelines.

## Why This Matters

These issues mean that some valid log formats cannot be parsed, format errors are discovered late, and the system may behave incorrectly on 32-bit platforms or with very large files.
