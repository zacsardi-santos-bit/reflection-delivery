Update the PRQL compiler to ensure that SQL generated for Snowflake includes a valid ORDER BY clause in window functions when none is specified by the user. Implement a fallback ordering mechanism to comply with Snowflake's requirements, while respecting any explicit user-defined sort orders.

*   Implement a fallback ORDER BY mechanism:
    *   When compiling a PRQL group+take query without an explicit sort targeting Snowflake, ensure the generated window function includes an ORDER BY clause using the numeric literal 1.
    *   If an explicit sort is provided by the user, use that column in the ORDER BY clause instead of the fallback.
    *   Only inject the fallback ORDER BY when no user-specified sort order exists.

*   Modify the DialectHandler trait:
    *   Add a method `requires_order_by_in_window_function() -> bool` in `prqlc/prqlc/src/sql/dialect.rs`.
    *   Set the default return value to false for all dialects.

*   Override the method in SnowflakeDialect:
    *   In the SnowflakeDialect implementation, override `requires_order_by_in_window_function()` to return true.

*   Update the translate_windowed function:
    *   Located in `prqlc/prqlc/src/sql/gen_expr.rs`, modify the function to check the dialect's `requires_order_by_in_window_function()` method.
    *   If it returns true and no ORDER BY expressions are specified by the user, inject a fallback ORDER BY expression using the numeric literal value '1'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.