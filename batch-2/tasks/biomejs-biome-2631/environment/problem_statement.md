## Description

When checking whether an array, string, or collection has any elements (or is empty), developers sometimes write implicit boolean checks against the length or size property instead of using an explicit comparison. These implicit checks work due to JavaScript's truthiness coercion, but they are less readable and less explicit about the developer's intent.

For example, writing a bare truthy check on a collection's length property to mean "if the array is non-empty," or a negated check on the size property to mean "if the map is empty," hides what is actually being tested. The linter should enforce that these checks always use an explicit comparison — greater-than-zero for non-empty checks and strict equality to zero for empty checks.

## Expected Behavior

A new lint rule should be added that:

- Detects when a length or size property is used as an implicit boolean and requires it be compared explicitly to zero
- Handles both truthy checks (non-empty: should compare as greater-than-zero) and falsy checks (empty: should compare as equal-to-zero)
- Recognizes common patterns that obfuscate the comparison, including double-negation, explicit boolean conversion wrapper calls, reversed operand order, and greater-than-or-equal-to-one idioms
- Provides an automatic fix for each detected violation
- Correctly handles cases where the fix requires inserting whitespace after a keyword to remain syntactically valid
- Does not flag cases where the comparison is already explicit, where the property is used in a non-boolean context, or where a number literal appears as the right-hand side of a logical OR

## Why This Matters

Explicit length comparisons make code easier to read and reason about. A reader immediately understands that a greater-than-zero comparison means "non-empty," whereas a bare property check requires knowing JavaScript's truthiness rules. Enforcing this consistently across a codebase reduces ambiguity and potential bugs.
