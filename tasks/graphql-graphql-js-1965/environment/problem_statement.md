## Description

GraphQL currently has no way to declare that a directive is allowed to appear more than once at the same location. Every directive is implicitly treated as non-repeatable, meaning the validation layer always rejects a document that applies the same directive twice to the same element — even if that directive is intentionally designed to be used multiple times.

We need a way to mark a directive definition as "repeatable" so that schema authors can explicitly opt in to allowing multiple applications of the same directive at a single location. The SDL parser should recognize an optional keyword in directive definitions indicating repeatability, the SDL printer should output it when set, and the schema type system should track whether each directive is repeatable.

## Expected Behavior

- A directive definition in SDL may include a keyword to mark it as repeatable, and parsing it produces an AST node with a boolean flag indicating repeatability.
- Non-repeatable directive definitions produce an AST node with the flag set to false.
- The type system representation of a directive has a boolean property for repeatability that is false by default.
- When the repeatability flag is explicitly set to true, the directive is considered repeatable.
- The SDL printer outputs the repeatable keyword for repeatable directives, preserving the flag in SDL roundtrips.
- The validation rule that checks for duplicate directives at a location must allow repeatable directives to appear more than once at the same location without reporting an error.
- Unknown directives (not present in the schema) must be silently ignored by the uniqueness validation rule rather than triggering an error.

## Why This Matters

Without repeatability support, directive-based APIs that need to stack or accumulate multiple annotations of the same directive on a single element are impossible to express or validate correctly in GraphQL. This change enables those patterns and ensures SDL documents roundtrip cleanly.
