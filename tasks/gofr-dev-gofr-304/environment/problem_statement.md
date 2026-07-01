## Description

The SQL database wrapper in this framework already implements a full set of database operations — querying, executing, preparing statements, starting and managing transactions, and performing health checks. However, the metrics interface used internally to record operation timing and statistics cannot be mocked for unit testing. As a result, no unit tests exist for the core database operations, and adding them causes build failures.

## Expected Behavior

- A mock implementation of the internal metrics interface should be available within the SQL package so that test code can set expectations on metrics calls.
- Unit tests for all database operations (queries, execution, prepared statements, transactions with commit and rollback, and health checks) should compile and pass.
- The mock should support setting expectations for histogram recording calls with a specific internal metric name and operation-type labels.
- Each database operation (including transaction variants) should record a histogram metric and emit a debug log message. The log prefix for each operation must match the operation name — with the exception that transaction-level execution, commit, and rollback each use distinct prefixes that identify them as transactional operations.

## Why This Matters

Without a mockable metrics interface, it is impossible to write isolated unit tests that verify that database operations correctly instrument themselves. This leaves core observability behavior untested, which is especially problematic as the framework evolves.
