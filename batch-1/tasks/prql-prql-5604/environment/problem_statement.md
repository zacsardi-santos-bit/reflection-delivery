## Description

When compiling PRQL queries that use interval/duration arithmetic (e.g., adding a number of days to a date column) targeting Snowflake, the compiler does not produce correct Snowflake-compatible SQL. Snowflake requires a specific interval expression format where the numeric value and the unit name are quoted together as a single string inside the interval expression. The compiler currently generates interval syntax that is incompatible with Snowflake's requirements.

## Expected Behavior

- Duration arithmetic in a PRQL query targeting Snowflake should compile to valid Snowflake SQL
- The interval expression must enclose both the numeric value and the unit name together inside a single-quoted string (e.g., the value 10 and unit DAY appear together as a quoted pair)
- The interval unit name must be rendered in uppercase singular form
- Column and table names in Snowflake output must use double-quoted identifiers
- A derived column that adds a duration to an existing column must appear in the SELECT clause as an aliased expression alongside all other columns

## Why This Matters

Users targeting Snowflake with PRQL need to perform date arithmetic using PRQL's built-in duration syntax. Without proper Snowflake interval formatting, queries involving date or time manipulation cannot be compiled to Snowflake-compatible SQL, making PRQL unusable for common date-offset operations in Snowflake workflows.
