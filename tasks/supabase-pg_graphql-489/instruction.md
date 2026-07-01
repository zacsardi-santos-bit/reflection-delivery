Implement an offset-based pagination feature in the GraphQL API to allow users to skip a specified number of records when querying collections. Ensure this feature works exclusively with forward pagination and is correctly reflected in the schema introspection.

*   Add an optional integer `offset` argument to collection query types in the GraphQL schema.
    *   The argument type must be scalar `Int` with kind: "SCALAR", name: "Int", ofType: null.
    *   Include the description: "Skip n values from the after cursor. Alternative to cursor pagination. Backward pagination not supported."
*   Ensure the `offset` argument functions correctly:
    *   When used with forward pagination (`first` and optionally `after`), skip the specified number of records from the beginning or from the cursor position.
    *   If `offset` is 0 or absent, set `hasPreviousPage` to `false` unless other conditions dictate otherwise.
    *   If `offset` is greater than 0, set `hasPreviousPage` to `true`.
    *   When combined with `last` or `before` (backward pagination), return an error with `data: null` and the message: `"offset" may only be used with "first" and "after"`.
*   Ensure compatibility with the existing `after` cursor argument, applying the offset relative to the cursor position.
*   Update pagination metadata:
    *   `hasNextPage` should accurately reflect the presence of additional records beyond the current page after applying both `offset` and `first`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.