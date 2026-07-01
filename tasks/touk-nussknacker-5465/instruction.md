Implement enhancements to the Nussknacker expression type system to address type-checking issues with conditional expressions and equality comparisons. Simplify the API for finding common supertypes by introducing predefined strategies and remove unnecessary configuration options.

*   Update `CommonSupertypeFinder`:
    *   Expose two predefined strategies: `CommonSupertypeFinder.Intersection` and `CommonSupertypeFinder.Default`.
    *   Remove `SupertypeClassResolutionStrategy` from the public API.
    *   Implement `CommonSupertypeFinder.Default` to:
        *   Provide `commonSupertype(left: TypingResult, right: TypingResult): TypingResult` without requiring `NumberTypesPromotionStrategy`.
        *   Merge fields from both records when computing the supertype of `TypedObjectTypingResult` values, assigning `Unknown` to fields with incompatible types.
        *   Fall back to the underlying object class for non-record types or incompatible tagged/literal values.
        *   Promote numeric types to their numeric supertype.
    *   Implement `CommonSupertypeFinder.Intersection` to:
        *   Include only fields present in both records for `TypedObjectTypingResult` values.
        *   Return `Typed.empty` for unrelated simple types.
        *   Return `Typed(Set.empty)` for incompatible tagged value types.

*   Modify `Typer` class:
    *   Remove `commonSupertypeFinder: CommonSupertypeFinder` from the constructor.
    *   Use `CommonSupertypeFinder.Default` for ternary operator and inline list element type inference.
    *   Use `CommonSupertypeFinder.Intersection` for arithmetic operators and equality checks.

*   Adjust ternary operator behavior:
    *   Produce `Unknown` type for incompatible branches.
    *   Merge records for branches with record types.
    *   Use a generic Map type for mixed record and map branches.

*   Ensure equality comparisons:
    *   Validate comparisons between records with different field sets or between a record and a generic map.

*   Remove strict type checking configuration:
    *   Eliminate `strictTypeChecking` from `ExpressionConfig` and related classes.

*   Ensure `TypedObjectTypingResult`:
    *   Set the value type parameter of the underlying Map class to `Unknown` for empty records or when no common supertype exists.

*   Validate nested JSON schema:
    *   Ensure the previously ignored test for nested JSON schema validation passes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.