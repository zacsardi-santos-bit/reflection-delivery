## Description

The Gleam compiler does not properly detect when the record update (spread) syntax is used on a type constructor that has no labelled fields. The update syntax is designed to create a modified copy of an existing record by spreading it and overriding specific named fields — but this only makes sense for constructors with at least one labelled field. When a constructor has only positional (unnamed) fields, using the update syntax is invalid.

Currently, if a programmer mistakenly writes record update syntax for such a constructor, the compiler either fails silently, produces a confusing error unrelated to the real problem, or panics. The programmer is left without actionable feedback.

## Expected Behavior

- When record update syntax is applied to a constructor with no labelled fields, the compiler should produce a clear error message explaining that the constructor has no labelled fields and that the update syntax requires at least one.
- This validation should work correctly both inside function bodies and inside constant definitions.

## Why This Matters

Without this validation, programmers can write code that looks syntactically valid but is semantically nonsensical, and the compiler fails to guide them toward the correct solution. A clear error at compile time makes the language safer and easier to use.
