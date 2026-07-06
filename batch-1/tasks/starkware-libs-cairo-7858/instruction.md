Update the block type inference logic to correctly handle diverging statements in code blocks. Ensure that blocks ending with loop continuations or calls to never-returning functions are typed as "never". Ignore item declarations following diverging statements when determining the block's type.

*   Implement logic in `crates/cairo-lang-semantic/src/expr/compute.rs` to ensure:
    *   Blocks with no tail expression and a last non-item statement that is a return or break statement are typed as `core::never`.
    *   Blocks with no tail expression and a last non-item statement that is a continue statement are typed as `core::never`.
    *   Blocks with no tail expression and a last non-item statement that is an expression with type `core::never` are typed as `core::never`.
    *   Blocks with no tail expression and item declarations following a diverging statement should be typed based on the diverging statement, not the items.
    *   Blocks with no tail expression and only item declarations or that are empty should be typed as `()` (unit).
    *   Blocks with no tail expression and a last non-item statement that is a let binding should be typed as `()` (unit).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.