## Description

SQLFluff currently cannot parse Snowflake SQL queries that use the ASOF JOIN syntax. Snowflake supports a specialized join type for time-series analysis that matches each row in one table to the most recent row in another table based on a timestamp comparison. This join type uses a dedicated condition clause to specify the time-matching comparison.

When a developer runs SQLFluff on a Snowflake query that contains this join type, the parser fails entirely — it cannot recognize the syntax and produces parse errors. This makes SQLFluff unusable for Snowflake codebases that rely on this feature.

## Expected Behavior

- Queries using this time-series join type should parse successfully with no errors
- The time-matching condition clause should appear as a distinct, named node in the parse tree
- The join may include an additional equality condition (ON clause), which should also be parsed correctly
- Multiple of these joins can be chained in a single query — all should parse
- This join type may be combined with standard join types in the same query
- Various comparison operators (greater than or equal to, greater than, less than) should all be supported inside the time-matching condition

## Why This Matters

Developers using Snowflake for time-series analysis frequently use this join type. Without parser support, SQLFluff cannot lint, format, or analyze any Snowflake SQL file that includes this join, blocking adoption for many Snowflake users.
