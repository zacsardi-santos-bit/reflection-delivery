## Description

The core SQL query-building library that tortoise-orm depends on has released a new version that redesigns how SQL is generated. The new version requires an explicit SQL context object to be passed when rendering SQL expressions, and it changed the way a database connection's quote character is retrieved. Existing tortoise-orm code still uses the old patterns and is therefore incompatible with the updated library.

## Expected Behavior

- The database backend's query class should expose a SQL context object that holds configuration like the quote character, so it can be retrieved directly instead of through an internal builder.
- SQL generation methods across tortoise-orm that override the underlying library's interfaces (for things like string wrapping, concatenation, raw SQL, subqueries, custom filters, and parameterized queries) should accept the new SQL context argument rather than arbitrary keyword arguments.
- All compound filter expressions — including equality, AND/OR combinations, negation, nested groups, blank filter handling, and annotation-based filters — should continue to produce the correct SQL strings when rendered with the updated library.

## Why This Matters

Because the old internal API patterns no longer exist in the new library version, any code that calls SQL generation methods or tries to look up the quote character will fail at runtime. Updating tortoise-orm to use the new patterns restores full compatibility, allowing all query building and database management operations to work correctly.
