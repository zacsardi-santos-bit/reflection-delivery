## Description

The ShardingSphere SQL parser for the SQLServer dialect does not support several valid SQL patterns that are commonly used in production SQLServer databases. Specifically:

1. **INSERT with table optimizer hints** — SQLServer allows specifying locking and performance hints in an INSERT statement using a WITH clause (e.g., to force table-level locking during a bulk insert). This syntax is not currently recognized by the parser, causing a parse failure.

2. **INSERT via stored procedure execution** — SQLServer supports inserting rows into a table by executing a stored procedure and capturing its result set. The parser does not handle this pattern when parameters are passed as named assignments.

3. **Complex SELECT expressions** — Certain SELECT queries that combine arithmetic operations with type casting applied to built-in system function calls are not parsed correctly.

4. **SELECT with aggregation, filtering, and sorting** — SELECT statements that join multiple tables, filter with compound conditions (including pattern matching), group results, filter groups with aggregation-based predicates, and sort the output are not always handled correctly for the SQLServer dialect.

## Expected Behavior

- INSERT statements that include a WITH clause containing optimizer hints should parse successfully and expose the hint information in the resulting AST.
- INSERT statements that execute a stored procedure (with named parameters) to populate a table should parse successfully.
- SELECT statements with arithmetic expressions mixing type casts and built-in function calls should parse successfully.
- SELECT statements using JOIN, compound WHERE, GROUP BY, HAVING with aggregation, and ORDER BY should parse successfully in the SQLServer dialect.

## Why This Matters

Without these fixes, ShardingSphere cannot be used as a proxy or query router for SQLServer databases that rely on these SQL patterns. Developers using performance-oriented insert patterns or complex analytical queries with SQLServer are completely blocked from using ShardingSphere in those environments.
