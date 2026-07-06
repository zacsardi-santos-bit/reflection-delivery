Implement two new code actions in the Gleam language server for pattern matching on structured values. These actions should automatically generate destructuring code for function arguments and let-binding variables, enhancing developer productivity by reducing manual pattern writing.

*   Implement a code action titled "Pattern match on argument":
    *   Offer this action when the cursor is on a function argument in both named and anonymous functions, including nested ones.
    *   Do not offer this action for arguments typed as empty tuples or for types marked internal from a different module.
    *   Ensure the action is available for internal types defined in the current module.
    *   For tuple arguments with elements, insert a pattern binding at the top of the function body using the format: `let #(value_0, value_1, ...) = arg_name`.
    *   For single-constructor custom types, insert `let ConstructorName(fields...) = arg_name` using 'value' for a single unlabelled field and 'value_0', 'value_1', etc., for multiple unlabelled fields. Use label shorthand for labelled fields.
    *   For types with multiple constructors, insert a case expression with one arm per constructor using the format: `case arg_name { Constructor1(...) -> todo ... }`.
    *   Respect constructor name qualification based on import context: use aliased names, unqualified names, or module-qualified names as appropriate.
    *   Properly reformat the function body if it is empty, converting it to a multi-line form with the inserted pattern.
    *   Preserve the indentation of existing statements when the function body is non-empty.

*   Implement a code action titled "Pattern match on variable":
    *   Offer this action when the cursor is on a variable name in a let binding.
    *   For tuple-bound variables, insert `let #(value_0, value_1, ...) = var_name` on a new line immediately after the let binding.
    *   For multi-constructor custom types, insert a case expression with one arm per constructor after the let binding.

*   Use the `PatternMatchOnValue` struct in `compiler-core/src/language_server/code_action.rs` to build and register these actions:
    *   Construct `PatternMatchOnValue` using the `new` method with parameters: `module`, `line_numbers`, `params`, and `compiler`.
    *   Call `code_actions()` on the `PatternMatchOnValue` instance to generate the actions.
    *   Register these actions in the language server engine by extending the actions list in the code action handler with the results from `code_actions()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.