## Description

The compiler currently only warns about always-deterministic comparisons when they appear inside assertion statements. However, the same kind of redundant comparison can appear anywhere in code — not just assertions — and the compiler stays silent about it.

For example, if a developer accidentally compares a variable to itself, or compares two different constructors of the same type that can never be equal, or compares two literal values whose result is obvious at compile time, there is currently no warning outside of assertion contexts. This makes it easy to miss copy-paste errors or dead logic that should have been caught earlier.

## Expected Behavior

- Any comparison expression where the result is statically determinable should emit a warning, regardless of whether it appears inside an assertion or as a standalone expression.
- The warning should clearly state whether the comparison always evaluates to true or always evaluates to false.
- This should cover: comparing a variable to itself, comparing record fields of the same variable instance, comparing different constructors of the same custom type, and comparing literal values (integers, strings, booleans, lists).
- Comparisons involving function calls should not produce a warning, since functions may have side effects that make the result unpredictable.
- The warning messages for redundant comparisons inside assertions should be updated to match the new consistent message format.
- The message for asserting a literal boolean (rather than a comparison) should be updated to be more specific and accurate.
- The warning for a discarded unused literal value should be updated to use clearer, more concise text.

## Why This Matters

Redundant comparisons are often signs of bugs — a developer comparing the wrong variable, forgetting to change one side of a condition, or leaving dead code. Catching these at compile time, not just in assertions, helps prevent subtle logical errors before they reach production.
