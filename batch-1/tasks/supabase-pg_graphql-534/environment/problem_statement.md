## Description

When a SQL function is defined with a parameter whose default value is a complex expression — such as a call to another SQL function, or an arithmetic expression — the GraphQL extension fails to expose that function properly. Instead of treating the parameter as optional (with a null default in GraphQL introspection), the system cannot parse the expression and incorrectly marks the parameter as required or excludes the function from the schema entirely.

## Expected Behavior

- SQL functions whose parameters have expression-based defaults (e.g., delegating to another function, or computing a value via arithmetic) should appear in the GraphQL schema.
- In GraphQL introspection, such parameters should show up in the field's argument list with a null default value — since the expression cannot be represented as a static GraphQL default.
- These functions should be callable with explicit argument values and return the correct result.
- These functions should also be callable *without* supplying the optional argument, with the database correctly applying the original default expression.

## Why This Matters

Developers commonly define SQL functions where default parameter values reference helper functions (e.g., to get the current user's ID) or computed values. Currently, such functions are broken in the GraphQL API — the parameter is treated as if it has no default, making the function unusable or incorrectly typed. This fix ensures that any SQL function with a well-formed default expression, however complex, is correctly exposed as an optional argument in the GraphQL schema.
