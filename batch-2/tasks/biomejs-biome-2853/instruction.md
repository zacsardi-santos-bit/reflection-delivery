Implement a new lint rule in the JavaScript analyzer to detect and correct "yoda expressions," where a literal value appears on the left side of a comparison operator. Ensure the rule provides an automatic fix to swap operands into the conventional order, maintaining logical correctness. Exclude certain patterns from being flagged, such as range-check patterns and comparisons involving only literals.

Requirements:

*   Implement the `NoYodaExpression` lint rule in the `nursery` group.
    *   The rule must detect binary comparison expressions with a literal on the left and a non-literal on the right.
    *   Supported operators: `==`, `===`, `!=`, `!==`, `<`, `>`, `<=`, `>=`.
*   Emit diagnostics with:
    *   Path: `lint/nursery/noYodaExpression`
    *   Error message: "Avoid the use of yoda expressions."
    *   Informational hint: "Yoda expressions can be confusing to some people, invert the expression operands for better readability."
*   Provide a safe auto-fix labeled "Flip the operators of the expression."
    *   Swap operands and invert ordering operators: `<` ↔ `>`, `<=` ↔ `>=`.
    *   Equality operators remain unchanged.
*   Exclude from flagging:
    *   Comparisons with both sides as pure literals.
    *   Bitwise and non-comparison operators.
    *   Range-check patterns with logical AND/OR connecting two comparisons of the same variable.
*   Handle complex expressions:
    *   Preserve parentheses and inline comments.
    *   Maintain `yield` and `await` with their operands.
    *   Wrap assignment expressions in parentheses.
    *   Preserve unary prefix operators with their operands.
*   Template literals with interpolations on the left are not treated as literals.
*   Register the rule in the following locations:
    *   Declare as `pub mod no_yoda_expression;` in `crates/biome_js_analyze/src/lint/nursery.rs`.
    *   List `NoYodaExpression` in the `declare_group!` macro in `crates/biome_js_analyze/src/lint/nursery.rs`.
    *   Register the category in `crates/biome_diagnostics_categories/src/categories.rs`.
    *   Add to `Nursery` struct in `crates/biome_configuration/src/linter/rules.rs` as `no_yoda_expression`.
    *   Create a type alias in `crates/biome_js_analyze/src/options.rs`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.