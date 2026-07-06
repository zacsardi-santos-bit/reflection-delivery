## Description

The Svelte template compiler incorrectly rejects valid template expressions that use the word "type" as a regular variable identifier. Expressions such as comparisons, arithmetic operations, and prototype chain or membership checks on a variable named "type" trigger a false positive error saying that declaration tags must use variable declaration keywords — even though these are perfectly valid JavaScript expressions, not type declarations at all.

Additionally, when a block comment appears inside a binary expression where "type" is the left operand (for example, between the operator and the right operand), the parser incorrectly classifies the whole expression as a type declaration, leading to a parse failure or misidentification.

A separate but related issue exists in the compiler's recovery/loose mode: when parsing an incomplete variable declaration tag whose right-hand side ends with a division operator and no right operand, the parser fails to produce a valid AST node for the partial declaration.

## Expected Behavior

- Template expressions using "type" as a regular identifier in binary operations (comparisons, arithmetic, prototype chain checks, membership checks, etc.) should be accepted without errors.
- A block comment appearing inside a binary expression involving "type" should not cause the expression to be misidentified as a type declaration.
- Actual TypeScript type alias declarations within declaration tags should still produce the appropriate error.
- The loose/recovery parser should gracefully handle incomplete variable declarations that end with a division operator.

## Why This Matters

Developers who happen to use a variable named "type" in their Svelte templates — a common variable name — get spurious compile errors that are difficult to understand. This is especially confusing in TypeScript projects where "type" has special meaning as a keyword but is also valid as an identifier in expressions.
