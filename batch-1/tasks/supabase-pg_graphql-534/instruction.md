Implement support for SQL functions with complex default expressions in the GraphQL schema. Ensure these functions are correctly exposed, callable, and introspectable in GraphQL, even when default values involve expressions.

*   Ensure SQL functions with complex default expressions appear in the GraphQL schema as queryable fields.
    *   Include functions with default values that call other SQL functions or use arithmetic expressions.
*   During GraphQL introspection:
    *   Display parameters with complex default expressions in the field's args list.
    *   Set the `defaultValue` to null for these parameters.
    *   Correctly report the parameter type and name.
*   Allow calling SQL functions in GraphQL with explicit argument values.
    *   Ensure the function returns the correct result based on provided values.
*   Allow calling SQL functions in GraphQL without providing optional arguments.
    *   Ensure PostgreSQL evaluates the SQL-level default expression and returns the correct result.
*   Apply these behaviors to all parameter types, including UUID, text, and integer, with function-call or arithmetic expression defaults.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.