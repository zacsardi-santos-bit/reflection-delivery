Implement a new code action in the Gleam language server to transform shorthand use expressions into their expanded form. Ensure the action is available when the cursor is within a use expression and that it correctly handles various cases of arguments and patterns.

*   Offer a code action titled "Desugar use expression" when the cursor is within a use expression.
*   Transformations:
    *   For `use <- func` or `use <- func()`, convert to `func(fn() {\n  <indented body>\n})`.
    *   For `use <- func(a, b)`, convert to `func(a, b, fn() {\n  <indented body>\n})`.
    *   For `use x <- func(a, b)`, convert to `func(a, b, fn(x) {\n  <indented body>\n})`.
    *   For `use x, y <- func(a, b)`, convert to `func(a, b, fn(x, y) {\n  <indented body>\n})`.
    *   For `use x: T, y: T <- func(a, b)`, convert to `func(a, b, fn(x: T, y: T) {\n  <indented body>\n})`.
*   Ensure the action targets the innermost use expression containing the cursor when nested expressions are present.
*   Do not offer the action for use expressions with complex patterns like tuples or literals.
*   Implement the code action in `compiler-core/src/language_server/code_action.rs`.
*   Wire the action into `compiler-core/src/language_server/engine.rs`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.