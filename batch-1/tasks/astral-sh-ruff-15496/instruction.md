Implement the intersection simplification logic in the type checker to correctly handle combinations involving a string literal type and truthiness constraints. Ensure that the simplifications are consistent regardless of the order of intersection members or the presence of an unknown type.

*   Simplify intersections involving string literal types:
    *   When intersecting a string literal type with an 'always truthy' constraint, exclude the empty string from the result.
    *   When intersecting a string literal type with a negation of 'always falsy', achieve the same simplification as with 'always truthy'.
*   Handle intersections involving unknown types:
    *   If an 'unknown' type is present with a string literal type and an 'always falsy' constraint, preserve the 'unknown' type and narrow the string component to only the empty string literal.
    *   If an 'unknown' type is present with a string literal type and a negation of 'always truthy', preserve the 'unknown' type and narrow the string component to only the empty string literal.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.