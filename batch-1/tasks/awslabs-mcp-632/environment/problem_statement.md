## Description

The Aurora DSQL MCP server's read-only query handler currently forwards all SQL queries to the database before performing any validation. The server relies entirely on the database's built-in read-only transaction mechanism to block writes, which means potentially harmful queries — data modification statements, SQL injection attempts, and multi-statement sequences designed to escape the read-only context — are transmitted to the database before being rejected.

This is a security gap: the server has no proactive defense at the application layer. A user or attacker submitting a write operation, injection payload, or transaction bypass trick will cause a full round trip to the database before the operation is stopped.

## Expected Behavior

- The server should validate SQL queries before opening any database connection.
- Write-modifying statements (inserts, updates, deletes, schema changes, permission grants, system operations) should be rejected immediately with a clear error explaining that write operations are not allowed.
- Queries containing SQL injection patterns (tautologies, UNION-based attacks, stacked queries, time-based probes, file read/write operations) should be rejected with an injection risk error.
- Queries that attempt to chain multiple statements in a way that could bypass the read-only transaction (including commit/rollback tricks) should be rejected.
- Safe, single-statement read-only queries should pass through and execute normally.
- When a query fails security validation, no database connection should be established at all.

## Why This Matters

Defense in depth: relying solely on the database to enforce read-only access means any misconfiguration or edge case at the database layer could allow a harmful operation through. Client-side enforcement provides an additional layer of protection and gives users faster, clearer feedback when a prohibited query is submitted.
