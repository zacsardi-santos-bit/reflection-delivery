## Description

When a database operation error is wrapped in an RPC-level error message and the error message contains embedded query bind variable data, the SQL error parsing logic incorrectly extracts the error code and SQL state. Specifically, when a bind variable value itself contains an error-like string (with an errno and sqlstate pattern), the parser picks up the wrong error code from deeper in the message instead of the correct one near the beginning.

## Example

Consider a duplicate primary key error (error 1062, sqlstate 23000) that gets wrapped in an RPC error message. If the SQL query being executed had a bind variable whose value happened to contain something like "(errno 1366) (sqlstate 10000)", the error parser would fail to correctly identify the outer error as a constraint violation. Instead, it would extract the wrong error number from the embedded bind variable data.

## Expected Behavior

- When an error message contains multiple errno/sqlstate patterns, the first (outermost) occurrence should be used to identify the SQL error code and state.
- A duplicate-key constraint violation should always be recognized as such, regardless of what data appears later in the error message string.

## Why This Matters

Callers that rely on the parsed error type to decide how to handle errors (e.g., retrying on transient errors vs. reporting constraint violations to the user) will receive incorrect error classifications, potentially causing incorrect retry behavior or suppressing meaningful error information.
