Implement support for multi-level field selection paths in the `@join` directive to allow nested joins through intermediate resolver types. Ensure that variables from the parent object and literal values can be used as arguments at any level of the nested path.

*   Update the `@join` directive to accept a `select` argument that supports nested field selection paths.
    *   Allow paths that span multiple type levels, such as `outerField(arg: $parentVar) { innerField(arg: $parentVar, literal: "value") }`.
*   Ensure the execution engine resolves each field in the nested selection path sequentially.
    *   Invoke the intermediate field's resolver with arguments derived from the parent object's fields.
    *   Use the result of the intermediate resolver as the parent context for the terminal field's resolver.
*   Implement variable resolution in join arguments using `$fieldName` syntax.
    *   Resolve these variables from the original parent object's fields at query execution time.
*   Ensure the final value returned from a nested join is the scalar or object value produced by the terminal resolver, not the intermediate type.
*   Validate that a GraphQL query selecting a field resolved via a nested `@join` returns the correct final value.
    *   For example, if a `User` type has a `greeting` field joined through an intermediate `Greetings` type, querying `{ user { greeting } }` should return the string produced by the terminal resolver using both the parent object's fields and any literal argument values specified in the join.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.