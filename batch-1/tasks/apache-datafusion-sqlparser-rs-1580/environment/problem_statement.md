## Description

The SQL parser currently has no support for table sampling syntax, which is used across many SQL dialects to query a statistical subset of rows from a table rather than the entire table. SQL statements that include table sampling clauses fail to parse entirely, making the parser unusable for any query that uses this feature.

Table sampling is commonly used in analytical workflows for performance testing, data exploration, and statistical analysis. Dialects like ClickHouse, Hive, Snowflake, and standard SQL all support sampling syntax, but the placement and options differ by dialect. For example, some dialects place the sampling clause before the alias, while others place it after.

## Expected Behavior

- The parser should recognize and handle table sampling clauses in the FROM clause
- Sampling specifications should support: a percentage or row count, a bucket-based specification (including an optional column expression for hashing), a sampling method keyword, optional seed or repeatability values, and optional offset
- The position of the sampling clause relative to the table alias should be configurable per dialect
- SQL statements containing sampling syntax should parse and serialize back to the original string (round-trip correctly)
- The parsed AST should expose the sample specification as part of the table reference node

## Why This Matters

Many real-world SQL queries from databases that support sampling fail to parse today. Adding this support means users can parse, analyze, and transform SQL from Snowflake, Hive, ClickHouse, and other systems without needing to manually strip out sampling clauses first.
