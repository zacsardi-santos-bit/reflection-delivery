## Description

When working with struct values in Rust, it's common to bind a struct to a simple variable name and then access its fields using dot notation throughout the function body. There is currently no automated refactoring action available to convert such a plain binding into a destructuring pattern. Developers who want cleaner, more idiomatic code must manually rewrite the binding and update all field access expressions by hand.

## Expected Behavior

- A new refactoring assist should be available that activates when the cursor is positioned on a variable bound to a struct value.
- Applying the assist should replace the plain variable binding with a destructuring pattern that names all the struct's fields directly.
- All subsequent uses of the original variable's field accesses in the same scope should be automatically updated to use the destructured field names.
- When a field access appears inside a reference expression, the reference qualifier should be preserved after the field name is substituted.
- For record structs where there are no name conflicts in scope, the shorthand destructuring syntax should be used.

## Why This Matters

This missing assist forces developers to manually perform a common and error-prone refactoring step. Having it automated reduces friction and helps produce more idiomatic Rust code that uses pattern matching at the binding site rather than repeated field access expressions.
