Implement a code action in the Gleam language server to convert assertive pattern bindings into case expressions. Ensure that the conversion is precise, handles all pattern forms, and maintains correct indentation. Additionally, fix the type mismatch error underline for alias patterns to cover the full aliased pattern.

*   Implement the `LetAssertToCase` struct in `compiler-core/src/language_server/code_action.rs` with the following methods:
    *   `new(module: &'a Module, params: &'a CodeActionParams) -> Self`
    *   `code_actions(self) -> Vec<CodeAction>`

*   Ensure the code action:
    *   Has the title "Convert to case" and kind `CodeActionKind::REFACTOR`.
    *   Is offered when the cursor is on a `let assert` statement, specifically between the `let` keyword and the start of the right-hand side expression.
    *   Converts the `let assert <pattern> = <expr>` statement to a case expression: `let <binding> = case <expr> { \n<indent>  <pattern> -> <value>\n<indent>  _ -> panic\n<indent>}`.
    *   Matches the indentation of the original statement.

*   Define the binding and value in the case expression based on the pattern:
    *   Use the single named variable directly if only one exists.
    *   Use a tuple `#(v1, v2, ...)` if multiple named variables exist.
    *   Use `_` and `Nil` if no named variables exist.

*   Handle specific pattern forms:
    *   Exclude discard-prefixed variables from the binding and return value.
    *   Include alias names in extracted variables.
    *   Include right-side assignment variables in string prefix patterns, and alias names if present.

*   Ensure the code action only converts the `let assert` statement under the cursor, leaving nested statements unchanged.

*   Fix the `Pattern::Assign` location in `compiler-core/src/ast.rs`:
    *   Ensure the location spans from the start of the inner pattern to the end of the alias name.

*   Update the AST visitor trait in `compiler-core/src/ast/visit.rs`:
    *   Expose `visit_typed_pattern` and per-variant visitor methods for all `TypedPattern` variants.
    *   Ensure `visit_typed_assignment` and `visit_typed_clause` invoke the pattern visitor on their respective patterns.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.