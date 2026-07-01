## Description

The predicate library contains a "matches everything" predicate that is currently named in a way that clashes with common programming idioms. The existing name suggests a collection operation — "check if all elements satisfy some condition" — rather than describing a predicate that is unconditionally true for every input. This naming ambiguity makes code harder to read and understand, especially for developers familiar with formal logic or type theory, where a universally true predicate has a well-established meaning.

## Expected Behavior

- The "matches everything" predicate should be renamed to use the standard formal-logic term for a predicate that is true for all inputs
- The rename should be applied consistently throughout the predicate package and any code that uses this predicate (task queue filtering logic, scope/slice operations, etc.)
- The predicate's behavior must remain unchanged: it still matches every value, supports equality comparison with other predicates of the same kind, and continues to interact correctly with the AND, OR, and NOT combinators

## Why This Matters

Using a name that aligns with mathematical and formal logic convention reduces confusion and clarifies the intent of the predicate for future maintainers. It avoids misleading readers into thinking the predicate performs a collection-style "all of these" check when it is actually a constant true predicate.
