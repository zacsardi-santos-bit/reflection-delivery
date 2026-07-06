## Description

When writing SQL queries that use optional named parameters inside nested function calls — a pattern commonly used for flexible, optional filtering — the code generator silently fails to include those parameters in the generated output. For example, a query that wraps an optional parameter inside a fallback expression, which is itself nested inside a pattern-matching expression, results in a generated parameter struct that is missing the expected fields entirely.

This means users who write queries with this kind of nesting end up with generated code that does not compile or does not accept the intended inputs, even though the SQL itself is valid and the intent is clear.

## Expected Behavior

- When a named optional parameter appears as an argument to a SQL function that is itself nested inside another SQL function, the code generator should still detect and include that parameter in the generated code.
- The generated parameter struct should contain all parameters used in the query, with the correct types inferred from context.
- This behavior should work correctly for both MySQL and PostgreSQL targets.

## Why This Matters

Optional filtering is a very common SQL pattern — users want to pass a value to filter by, or skip filtering if the value is absent. When this involves nested function calls (as is required for some databases and use cases), the current behavior breaks generated output entirely. Users have no workaround except to restructure their queries in ways that may not be semantically equivalent.
