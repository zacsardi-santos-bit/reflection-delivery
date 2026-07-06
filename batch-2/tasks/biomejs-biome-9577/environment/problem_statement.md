## Description

When calling array reduction methods in TypeScript, it is common to cast the initial accumulator value to a specific type using a type assertion. This pattern silences TypeScript's type checker in a way that can hide real type errors: the checker accepts the assertion instead of verifying that the reducer callback actually returns the correct type. The recommended approach is to pass the accumulator type directly as a type parameter on the reduction call, which allows TypeScript to fully validate the callback's return type.

Currently, Biome has no lint rule that catches this pattern and guides developers toward the safer, type-parameter-based approach. A rule should be added to detect when the initial value of a reduction call uses a type assertion and suggest converting it to use a type parameter instead.

## Expected Behavior

- When a reduction call's initial value uses a type assertion in any of its syntactic forms (including parenthesized forms), a diagnostic should be emitted.
- The diagnostic should apply to both the forward and reverse variants of the array reduction method.
- It should apply to all asserted types: simple types, arrays, tuples, union types, intersection types, and generic types.
- It should also work correctly when the call already has a type parameter but the initial value still carries a redundant assertion — this was previously a false negative that went undetected.
- An automatic unsafe fix should be provided that either moves the type from the assertion to a type parameter (when no type parameter exists) or simply removes the redundant assertion (when a type parameter is already present).
- Calls without an initial value, with an initial value that has no assertion, or that already correctly use a type parameter without an assertion should not be flagged.
- Using a type narrowing/constraint operator (which has different semantics from a type assertion) on the initial value should also not be flagged.

## Why This Matters

Type assertions on accumulator initial values silently bypass TypeScript's type checking in a way that can introduce subtle bugs. Developers who want their reducer callbacks to be fully type-checked have no automated guidance today. This lint rule provides that guidance and offers an automatic fix, making it easy to migrate to the safer pattern.
