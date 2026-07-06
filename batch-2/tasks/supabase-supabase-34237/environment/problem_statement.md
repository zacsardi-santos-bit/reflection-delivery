## Description

The table data viewer in the database management tool needs a dedicated SQL generation module that safely handles all the edge cases when fetching rows from any type of database object (tables, views, materialized views, foreign tables).

Currently, array columns — especially arrays of custom enum types — are not handled correctly when generating the row-fetch SQL. Large text values and large arrays can also cause memory issues or crashes in the browser. Additionally, column names containing special characters (spaces, hyphens, embedded quotes) can produce broken SQL.

## Expected Behavior

- Fetching rows from a table, view, materialized view, or foreign table should all work through a single, unified SQL generation function.
- Text and JSON column values that exceed a character threshold should be automatically truncated in the query output, with an ellipsis suffix to indicate truncation.
- Array columns (including arrays of custom enum types) that exceed the character threshold should have their elements limited to a maximum count, with a truncation sentinel string appended to the result array to signal truncation.
- Arrays that are within the size threshold should be returned as-is, cast to a text array.
- Non-text, non-array columns (numbers, booleans, timestamps, enums, etc.) should be returned unmodified.
- Column names with spaces, dashes, or quotes should be correctly escaped.
- The function should support optional filters, sort specifications, and page-based pagination.
- Custom character limits and array size limits should be configurable as parameters.

## Why This Matters

Without proper truncation at the database query level, very large text or array values cause excessive memory allocation on the JavaScript side — potentially crashing the client. Handling this in the generated SQL keeps data transfer lean and the UI stable, even when tables contain columns with large or unbounded content.
