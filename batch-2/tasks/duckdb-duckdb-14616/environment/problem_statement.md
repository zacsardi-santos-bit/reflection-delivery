## Description

DuckDB's asynchronous ("pending") query API currently does not support passing bound parameter values when creating a pending query. This means developers who want to use parameterized queries — which prevent SQL injection and allow efficient re-execution with different values — cannot do so through the pending execution path. Instead, they are forced to either format values directly into the query string or use a different execution pathway entirely.

Additionally, when a prepared statement is executed and some parameter values are missing, the current error messages are not informative enough. The existing messages either describe a count mismatch in vague terms or report a mismatch without identifying which parameters are missing. This makes it harder for developers to quickly understand and fix the issue.

## Expected Behavior

- It should be possible to create a pending (deferred) query by supplying both a parameterized query string and a vector of bound values in a single call. The pending result should be executable and return correct results.
- Catalog-level errors (e.g., referencing a non-existent table) should be reported immediately on the pending result itself.
- Value type incompatibilities should be surfaced when the pending result is actually executed.
- Pending queries with bound parameters should work correctly with transactions, respecting isolation between concurrent connections.
- When a prepared statement is run with fewer values than required, the error message should clearly identify which parameter numbers are missing their values, rather than just reporting a count mismatch.

## Why This Matters

Parameterized queries are a key tool for safe and efficient database access. Without support in the pending query API, developers using asynchronous or streaming workflows are blocked from using parameterized queries safely. Clearer error messages reduce debugging time when parameters are accidentally omitted.
