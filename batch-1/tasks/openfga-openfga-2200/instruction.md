Improve the authorization check system to efficiently handle recursive hierarchical permission models using a fast-path evaluation for specific union patterns. Implement methods and object providers to classify relations and invoke the fast-path algorithm, ensuring correct and efficient authorization checks.

*   Implement the `IsRelationWithRecursiveTTUAndAlgebraicOperations` method in `pkg/typesystem/typesystem.go`:
    *   Return `true` if the relation has infinite weight for the given user type, has one outgoing edge to a union node, and all non-recursive branches have weight ≤ 1.
    *   Return `false` if the parent relation resolves to more than one type, includes a userset reference, contains multiple TTU edges, or any non-recursive branch has weight > 1.
    *   Ensure it returns `true` when `RecursiveTTUCanFastPath` does for the same inputs.

*   Implement the `recursiveTTUFastPathUnionAlgebraicOperations` method in `internal/graph/check_fast_path.go`:
    *   Accept a context, `ResolveCheckRequest`, `TupleToUserset` rewrite, and `TupleKeyIterator`.
    *   Return a `ResolveCheckResponse` with the correct allowed value and a `nil` error on success.

*   Develop the `newSimpleRecursiveObjectProvider` function in `internal/graph/object_providers.go`:
    *   Return an error if the `TypeSystem` or `RelationshipTupleReader` is `nil`.
    *   Implement the `Begin` method to handle `nil` requests, return a closed channel on empty iterators, and stream `usersetMessage` values for results.

*   Develop the `newComplexTTURecursiveObjectProvider` function in `internal/graph/object_providers.go`:
    *   Return an error with "nil typesystem" if the `TypeSystem` is `nil`.
    *   Return an error if the `rewrite` is `nil` or not a `TupleToUserset`.
    *   Implement the `Begin` method to handle `nil` requests, unrecognized object types, and propagate iterator errors through `usersetMessage`.

*   Ensure end-to-end authorization checks return correct boolean results for models using recursive TTU and algebraic operations, including wildcards, multiple non-recursive branches, and parenthesized intersections.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.