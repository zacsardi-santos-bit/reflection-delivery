Implement a fix for the SIM108 lint rule to ensure that f-string quote styles are preserved when converting if-else assignment blocks into ternary expressions. Update relevant code to maintain the original quote style of f-strings, whether single or double quotes, during auto-fix operations.

*   Preserve f-string quote styles in ternary expressions:
    *   Ensure that when converting an if-else block with an f-string using double quotes, the resulting ternary expression maintains the double-quote style.
    *   Ensure that when converting an if-else block with an f-string using single quotes, the resulting ternary expression maintains the single-quote style.

*   Modify the FStringFlags struct:
    *   Implement a public constructor named `empty()` in `crates/ruff_python_ast/src/nodes.rs` for the `FStringFlags` struct.
        *   Signature: `FStringFlags::empty() -> FStringFlags`
        *   Functionality: Return an instance of `FStringFlags` with no flags set, equivalent to `FStringFlagsInner::empty()`.
        *   Ensure `FStringFlags::empty()` is callable as a static/associated function on `FStringFlags`.

*   Update test normalizer:
    *   In `crates/ruff_python_formatter/tests/normalizer.rs`, modify the construction of `FString` nodes.
        *   Use `FStringFlags::empty()` instead of `FStringFlags::default()` to prevent overwriting f-string quote-style information during normalization.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.