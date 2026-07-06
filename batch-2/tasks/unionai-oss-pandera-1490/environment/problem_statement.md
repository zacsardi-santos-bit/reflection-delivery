## Description

When schema validation fails in lazy mode, the error report is a flat count-based summary message with no structure distinguishing between different categories of violation. It's impossible to tell at a glance which errors are about the shape or type of the data (structural schema violations) versus which are about the actual values in the data (check failures). There is also no way to restrict validation to run only one of these categories.

## Expected Behavior

- The error object produced by lazy validation should expose a structured report that groups failures into two distinct categories: schema-level errors (wrong types, missing or extra columns, null violations) and data-level errors (values failing checks).
- It should be possible to configure the validation system to run only schema-level checks, only data-level checks, or both.
- When validation is restricted to one category, the error report should contain only the relevant section — with no entries from the excluded category.
- The error count summary should use plain string keys rather than internal enum objects, making it easy to inspect programmatically.
- Individual check failure messages should clearly identify the column, the check, and the offending values in a consistent human-readable format.
- When columns or indices are out of order in an ordered schema, the error message should explicitly state that they are out of order.
- Missing required columns should raise a specific, well-typed schema error rather than a generic exception.

## Why This Matters

This structured error format makes it much easier to programmatically filter, display, or act on validation failures. Users who only care about structural problems (e.g., during schema evolution) can skip data checks entirely. Users who only care about data quality can skip structural checks. The improved error messages also make debugging faster by clearly describing what went wrong and where.
