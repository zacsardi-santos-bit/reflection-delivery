## Description

The implicit return lint rule incorrectly flags functions that end a code branch by calling a function annotated as "never returning" when that annotation comes from a widely-used type extensions library rather than the standard library.

When a function has a declared return type but one of its branches terminates by calling another function that provably never returns normally (because its return annotation says so), no explicit return statement is needed in that branch. The lint rule correctly handles the standard-library "no return" annotation today, but fails to recognize the equivalent annotation from the type extensions library — causing false-positive warnings for users of that library.

## Expected Behavior

- A function should not be flagged for a missing explicit return when a branch ends by calling any top-level function whose return type annotation indicates it never returns, regardless of whether that annotation comes from the standard library or the type extensions library.
- Both forms of the "never returns" annotation are semantically equivalent and should be treated identically by the rule.
- As a known limitation, nested functions (defined inside the same function body) annotated as never-returning are not yet handled — the rule may still fire in those cases, and this is acceptable pending a future improvement.

## Why This Matters

Users who write functions using the type extensions library's equivalent annotation style currently receive spurious lint warnings on otherwise-correct code. This creates noise and may lead developers to add unnecessary boilerplate to suppress the false positives.
