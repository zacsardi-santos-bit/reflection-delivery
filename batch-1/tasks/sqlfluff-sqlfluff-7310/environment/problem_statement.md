## Description

When writing BigQuery SQL that uses type conversion expressions with format specifiers — a feature that lets you convert values between types using a specific format pattern (such as formatting a date as a string with a particular template, or parsing a string into a typed value) — the SQL parser does not handle these expressions correctly.

Currently, the format indicator within these conversion expressions is being misidentified as a column reference rather than recognized as a dedicated language keyword. This results in an incorrect parse tree that doesn't accurately represent the SQL structure.

Additionally, several valid variants of this syntax are not supported at all, including conversions involving date, time, timestamp, and numeric format patterns, as well as an optional timezone specification that can follow the format string.

## Expected Behavior

- The format specifier inside a type conversion expression should be recognized as a keyword, not as a column reference.
- The format string that follows should appear directly in the parse tree as a quoted literal, not wrapped inside an expression node.
- The following conversion patterns should all parse correctly:
  - String to binary format with a format pattern
  - Date value to string with a date format pattern
  - Time value to string with a time format pattern
  - Timestamp value to string with a format pattern and optional timezone clause
  - String to time value using a time format pattern
  - Column reference to string using a numeric format pattern (including patterns with special characters)
  - Numeric literals (positive and negative) to string using numeric format patterns

## Why This Matters

BigQuery supports rich type conversion syntax with format strings, and developers writing or linting BigQuery SQL expect the parser to correctly understand these expressions. The current behavior produces incorrect parse trees and fails to support many valid BigQuery SQL patterns, making the linter unreliable for real-world BigQuery code.
