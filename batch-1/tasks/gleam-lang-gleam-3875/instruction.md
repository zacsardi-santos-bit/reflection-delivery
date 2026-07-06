Implement a code action in the Gleam language server to automatically convert inexhaustive pattern assignments into case expressions. Ensure that the action is only available for inexhaustive patterns and that the generated code respects the surrounding code's indentation. The action should handle all pattern types and only apply to the targeted assignment when nested.

*   Offer a code action named 'Convert to case' of kind QUICKFIX with preferred set to true when the cursor is on an inexhaustive let pattern assignment.
*   Ensure the code action only appears for inexhaustive patterns, not for exhaustive ones.
*   Replace the inexhaustive let assignment with a case expression:
    *   Bind the result to a new let assignment.
    *   The first branch must match the original pattern and return the bound variables.
    *   Append missing patterns as additional branches returning 'todo'.
*   Handle variable bindings:
    *   Exclude variables starting with '_' (discard variables) from the result binding.
    *   Use 'let _ =' and return 'Nil' when there are zero non-discard variables.
    *   Use 'let varname =' and return the variable name for exactly one non-discard variable.
    *   Use 'let #(v1, v2, ...)' and return '#(v1, v2, ...)' for two or more non-discard variables.
*   Match the indentation of the generated case expression with the original let assignment:
    *   Indent each case clause two spaces deeper than the case keyword.
*   Apply the code action independently to the let assignment overlapping the cursor selection range when nested.
*   Implement the code action in the function `code_action_inexhaustive_let_to_case` in `compiler-core/src/language_server/code_action.rs`.
    *   Signature: `pub fn code_action_inexhaustive_let_to_case(module: &Module, line_numbers: &LineNumbers, params: &CodeActionParams, error: &Option<Error>, actions: &mut Vec<CodeAction>)`
    *   Ensure this function is called from the code action dispatch in `compiler-core/src/language_server/engine.rs`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.