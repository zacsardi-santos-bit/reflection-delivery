## Description

When an INSERT statement reads client-provided data through the special one-shot streaming table function, wraps it in a Common Table Expression, and then references that CTE more than once in the same query (for example, joining two CTE aliases derived from the same underlying CTE in a cross-join), ClickHouse behaves incorrectly:

- In **debug builds**, the server crashes with an internal error that kills the process.
- In **release builds**, the INSERT silently discards the client's data — rows are lost without any warning.

Neither outcome is acceptable. Users have no way of knowing that the streaming source can only be consumed once.

## Expected Behavior

- Any query that would attempt to read the streaming source a second time should be **rejected early** with a clear, specific error indicating that the source is a one-shot stream.
- The error should be distinct from internal logic errors, so users understand it is a usage problem, not a server bug.
- Queries that reference the CTE only once should continue to work normally.
- Using the streaming function directly without a CTE should continue to work.
- As a supported workaround, using a materialized CTE (with the appropriate settings enabled) should allow multiple references to the same data.
- After rejecting such a query, the server must remain alive and stable.

## Why This Matters

Users may naturally try to reference the same CTE more than once — for example, to join different columns from the same input data. The current behavior either silently corrupts data or crashes the server, making it very hard to diagnose. A clean rejection with a helpful error message allows users to understand the limitation and apply the documented workaround.
