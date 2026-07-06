Implement the renaming of the "matches everything" predicate in the predicate library to align with formal logic conventions. Ensure that the new naming is applied consistently across the entire package and that the predicate's behavior remains unchanged.

*   Implement a generic function `Universal[T any]()` in the `common/predicates` package within a file named `universal.go`.
    *   This function should return a `Predicate[T]`.
*   Define the underlying concrete type as `UniversalImpl[T any]` in `universal.go`.
    *   Ensure the `Test(t T) bool` method always returns `true`.
    *   Ensure the `Equals(predicate Predicate[T]) bool` method returns `true` only if `predicate` is of type `*UniversalImpl[T]`.
*   Update the `And` combinator to treat `Universal` predicates as identity elements.
    *   If all operands are `Universal`, the result must be a `Universal` predicate.
*   Update the `Or` combinator to short-circuit and return the `Universal` predicate immediately when encountered.
*   Ensure the `Not` function applied to a `Universal` predicate returns an `Empty` predicate.
*   Ensure the `Empty` predicate's `Equals()` method returns `false` when compared to a `Universal` predicate.
*   Ensure the `Not` predicate's `Equals()` method returns `false` when compared to a `Universal` predicate.
*   Update all existing usages of the former `All()` function and `AllImpl` type in the predicates package, including files like `and.go`, `not.go`, and `or.go`, to use `Universal()` and `UniversalImpl` respectively.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.