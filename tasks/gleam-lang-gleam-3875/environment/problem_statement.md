## Description

The Gleam language server should offer a code action to automatically convert an inexhaustive pattern assignment into a case expression. Currently, when a developer writes a pattern assignment that doesn't cover all possible values, the compiler correctly reports a type error but the language server provides no automated fix. The developer is left to manually restructure the code.

## Expected Behavior

- When the cursor is placed on an inexhaustive pattern assignment, a "Convert to case" code action should be available.
- Accepting the action should replace the assignment with a complete case expression that:
  - Binds the result to the extracted non-discard variables from the original pattern
  - Includes the original pattern as the first branch, returning the bound variables
  - Appends all missing patterns as additional branches with placeholder values
- If all variables in the pattern are discards (or there are none), the result binding should use a wildcard and the matched branch should return a unit value
- If exactly one non-discard variable is present, it should be bound directly
- If multiple non-discard variables are present, they should be collected into a tuple binding
- The generated code should respect the indentation level of the surrounding code
- When multiple inexhaustive assignments are nested, only the targeted assignment should be converted
- The code action should NOT appear for exhaustive patterns (such as tuple patterns that fully cover all cases)

## Why This Matters

This saves developers significant time and reduces the chance of mistakes when converting unsafe inexhaustive pattern assignments into properly-handled case expressions. The action handles all pattern types: constructors, lists, tuples, bit arrays, string prefixes, and aliases.
