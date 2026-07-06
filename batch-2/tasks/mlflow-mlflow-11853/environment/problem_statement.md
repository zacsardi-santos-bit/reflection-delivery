## Description

MLflow already supports creating and retrieving individual traces for experiment tracking, but there is no way to search or filter across multiple traces programmatically. Users need to be able to query traces the same way they can query runs — by filtering on attributes like name, status, timestamps, duration, tags, and associated run ID — and sort the results by those same fields.

## Expected Behavior

- Users should be able to search for traces across one or more experiments using a filter expression
- Filter expressions should support matching on trace name, status, start time, execution duration, tags, and source run
- Results should be sortable by any of these attributes, including tag values, with null values sorting after non-null ones
- When no sort order is specified, traces should be returned newest-first (descending by start time)
- When traces share the same start time, results should be ordered deterministically so that pagination is stable and consistent
- Large result sets should be paginated, with a token returned to retrieve the next page
- Invalid pagination sizes (too large or negative) should be rejected with a clear error message

## Why This Matters

Without search support for traces, users must retrieve and filter traces manually in application code, which is inefficient and does not scale to large experiments. Adding this feature brings trace querying to parity with existing run search functionality and enables programmatic trace analysis workflows.
