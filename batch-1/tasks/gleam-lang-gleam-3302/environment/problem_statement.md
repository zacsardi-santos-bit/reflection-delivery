## Description

The Gleam language server should provide a refactoring code action to convert assertive pattern binding statements into explicit case expressions. This is useful when a developer wants to make error handling more visible, add extra match branches, or simply work with a different style of code.

Currently the language server does not offer any automated way to perform this conversion, so developers must rewrite these statements by hand.

## Expected Behavior

- When the cursor is on an assertive pattern binding, the language server offers a "Convert to case" refactoring action.
- The action rewrites the statement so that the right-hand side expression is matched in a case block, with the original pattern as the first branch and a wildcard fallback branch that panics.
- The extracted binding on the left-hand side should only include the meaningful named variables from the pattern. Discard-prefixed variables should not be included.
- When only one named variable is present, it should be bound directly. When multiple named variables are present, they should be collected into a tuple. When no named variables are present at all, the binding should be a discard and the matching arm should return a unit-like value.
- The case body should be indented correctly based on the column position of the original statement.
- When assertive pattern bindings are nested, the action should only convert the one under the cursor — leaving others unchanged.
- The action must handle all pattern forms: constructors, lists, tuples, bit arrays, string prefix patterns, and aliases.

## Bug Fix

Additionally, a type mismatch error in alias patterns within case expressions currently underlines one fewer character than it should. The error underline should span the full extent of the aliased pattern, including the alias name.

## Why This Matters

Developers who want more control over their pattern matching, or who are migrating code to handle errors more explicitly, currently have no automated tooling support. This refactoring action makes that workflow faster and less error-prone.
