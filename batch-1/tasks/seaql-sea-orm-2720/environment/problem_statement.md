## Description

When defining a partial model that includes active enum fields and specifies a table alias, the generated SQL query is missing the required type cast for those enum columns. Without the fix, enum fields in partial models with a table alias are selected without the proper database-level cast (e.g., from an enum type to text on PostgreSQL), even though the cast is correctly applied when no table alias is specified.

## Expected Behavior

- When a partial model is annotated with a table alias at the struct level, enum-typed fields should still be wrapped in the appropriate type cast expression in the generated SQL.
- When a partial model field references a column from the entity under a different output name (via an explicit column mapping), the generated SQL should use the table alias for column qualification and apply the correct enum-to-text cast.
- When a struct-level alias is combined with a nested partial model that has its own alias, each section of the query should use the correct alias and still apply the enum cast.

## Why This Matters

This bug means that queries involving partial models with both enum fields and table aliases produce incorrect SQL on PostgreSQL, which can cause runtime errors or silently return wrong data. Developers using table aliasing with partial models containing enum columns need the type cast to be consistently applied regardless of whether a table alias is in use.
