## Description

When users run SELECT queries in the SQL editor against large tables, the editor can accidentally return an enormous number of rows. There's currently no automatic safety mechanism to cap results. We need utility functions that can determine whether a row limit should be automatically appended to a query, and that can produce a properly formatted SQL string with the limit applied.

## Expected Behavior

- Given a SQL query and a desired row limit, the system should be able to decide whether it is safe and appropriate to auto-append a limit to the query.
- Auto-appending should only happen for simple, single SELECT statements — not for non-SELECT queries, queries that already include a row limit, queries with multiple statements, or queries containing comments.
- A limit of zero or below should never trigger auto-appending.
- A helper that applies the limit to a SQL string should strip any trailing semicolons from the original query and append the limit clause followed by a single semicolon, regardless of how many semicolons the original query ended with.

## Why This Matters

Without this guard, a developer or end user running a broad SELECT on a multi-million row table in the SQL editor could cause significant slowdowns or even browser crashes. Automatically appending a sensible row limit for qualifying queries makes the editor safer and more predictable by default, while respecting the user's intent when they have already specified a limit or are running something other than a simple SELECT.
