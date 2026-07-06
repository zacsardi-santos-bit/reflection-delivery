Implement the functionality to include locally defined variables in autocompletion suggestions within the Gleam language server. Ensure that these suggestions respect lexical scoping and provide relevant details for each variable.

*   Update the language server to generate completions inside a function body:
    *   Collect locally defined variables in scope at the cursor position.
    *   Include these variables as completion items.
*   Ensure each local variable completion item includes:
    *   Kind set to `Variable`.
    *   Sort text prefixed with '2_' followed by the variable name.
    *   Detail set to the variable's inferred type string.
    *   Documentation set to "A locally defined variable."
    *   Text edit inserting the variable name.
*   Include function parameters as local variable completions throughout the function body.
*   Include variables bound by let expressions appearing before the cursor position.
    *   Exclude variables bound after the cursor position.
*   Restrict anonymous function arguments to appear only within that function's body.
    *   Ensure they are not visible in the enclosing outer scope.
*   When inside a nested anonymous function:
    *   Outer function parameters remain visible.
    *   Sibling or child anonymous function parameters not enclosing the cursor are not visible.
*   Include variables bound by case expression patterns within the corresponding case branch body.
    *   Include both sides of an 'as' pattern alias in a let binding as separate completion items.
*   Ensure variables from destructuring patterns appear as completions:
    *   Include tuple, list, bit-array, and string-prefix patterns.
*   Exclude variables with names starting with an underscore (discard bindings) from completion results.
*   Do not provide local variable completions when the cursor is within a function's parameter declaration list.
*   Make the `Function` AST node's `full_location` method publicly accessible for determining the top-level function containing the cursor.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.