Implement a new field-level directive in your GraphQL schema engine to support composable resolver expressions, including conditional branching. Ensure the directive can handle existing resolver types and a new conditional variant, while providing precise validation and error messages.

*   Introduce a new field-level directive that accepts a `body` argument containing a resolver expression.
    *   Ensure fields with this directive are recognized as having a resolver.
*   The `body` argument must support the following expression variant keys: `http`, `grpc`, `graphQL`, `const`, and `if`.
    *   Produce a validation error if an unrecognized variant key is used.
*   Handle the `if` expression variant with three required sub-fields: `cond`, `then`, and `else`.
    *   Validate the presence of all three sub-fields, producing specific error messages if any are missing.
*   Provide specific validation error messages:
    *   Missing `body` argument: "Parsing failed because of missing field `body`" with trace ["@expr"].
    *   Unknown variant key: "Parsing failed because of unknown variant `<name>`, expected one of `http`, `grpc`, `graphQL`, `const`, `if`" with trace ["@expr", "body"].
    *   Missing `cond` field in `if`: "Parsing failed because of missing field `cond`" with trace ["@expr", "body", ".", "if"].
    *   Missing `then` field in `if`: "Parsing failed because of missing field `then`" with trace ["@expr", "body", ".", "if"].
    *   Missing `else` field in `if`: "Parsing failed because of missing field `else`" with trace ["@expr", "body", ".", "if"].
*   Ensure runtime evaluation of `if` expressions:
    *   Return the result of the `then` branch if the condition is truthy.
    *   Return the result of the `else` branch if the condition is falsy.
*   Ensure server-side SDL round-trips correctly, maintaining the identity property.
*   Strip the directive from client-facing SDL, presenting fields as plain without resolver annotations.
*   Update `tests/graphql_spec.graphql` to declare the `@error` directive as `repeatable on OBJECT`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.