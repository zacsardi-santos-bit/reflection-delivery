Implement a code action in the Gleam language server to convert the first step of a pipeline expression into an equivalent regular function call. Ensure the action is available when the cursor is on or near a pipeline expression and handles various pipeline forms, converting only the first step while leaving subsequent steps intact.

*   Register the code action:
    *   Title: "Convert to function call"
    *   Kind: `CodeActionKind::REFACTOR_REWRITE`
    *   Location: `compiler-core/src/language_server/engine.rs`
    *   Ensure it is registered alongside other code actions.

*   Implement the `ConvertToFunctionCall` struct:
    *   Location: `compiler-core/src/language_server/code_action.rs`
    *   Description: Rewrites the first step of a pipeline expression into a direct function call.
    *   Implement the language server Visit trait to traverse the typed AST and identify pipeline expressions.
    *   Apply text edits to perform the conversion.

*   Define the struct with the following signatures:
    *   `new(module: &'a Module, line_numbers: &'a LineNumbers, params: &'a CodeActionParams) -> Self`
    *   `code_actions(mut self) -> Vec<CodeAction>`

*   Handle pipeline forms:
    *   Implicit first-argument insertion: Convert `value |> fn(other_args)` to `fn(value, other_args)`.
    *   Bare function reference: Convert `value |> fn` to `fn(value)`.
    *   Function reference with empty parentheses: Convert `value |> fn()` to `fn(value)`.
    *   Module-qualified function reference: Convert `value |> module.fn` to `module.fn(value)`.
    *   Function returning another function: Convert `value |> fn(arg)` to `fn(arg)(value)`.
    *   Explicit hole in the first position: Convert `value |> fn(_, other)` to `fn(value, other)`.
    *   Explicit hole in a non-first position: Convert `value |> fn(first_arg, _)` to `fn(first_arg, value)`.

*   For pipelines with multiple steps, convert only the first step:
    *   Example: Convert `value |> fn1(a) |> fn2(b)` to `fn1(value, a) |> fn2(b)`.

*   Export the `ConvertToFunctionCall` struct from `code_action.rs` and import it in `engine.rs`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.