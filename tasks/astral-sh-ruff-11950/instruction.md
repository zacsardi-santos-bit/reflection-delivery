Ensure that the linter continues processing and reporting all violations in a Python file, even if syntax errors are present. Implement a new token iterator to manage parsing context and update test cases to verify the changes.

*   Update the token iteration infrastructure:
    *   Replace `up_to_first_unknown()` with `tokens.iter()` or `tokens.iter_with_context()` to iterate over all tokens.
    *   Implement `TokenIterWithContext` in `crates/ruff_python_parser/src/lib.rs` to track nesting levels and handle syntax errors.
        *   Increment nesting on `Lpar`, `Lbrace`, `Lsqb` tokens.
        *   Decrement nesting on `Rpar`, `Rbrace`, `Rsqb` tokens.
        *   Reset nesting to 0 on `Newline` if nesting is greater than 0.
        *   Implement methods: `nesting() -> u32`, `in_parenthesized_context() -> bool`, `peek() -> Option<&Token>`.
        *   Implement `Iterator<Item = &Token>` and `FusedIterator`.
        *   Add `Tokens::iter_with_context() -> TokenIterWithContext`.

*   Update specific checkers to use the new iterator:
    *   Modify `LinePreprocessor` in `blank_lines.rs` and compound statements checker to use `tokens.iter_with_context()` and `token_iter.in_parenthesized_context()`.

*   Register new test cases:
    *   In `crates/ruff_linter/src/rules/flake8_implicit_str_concat/mod.rs`, add:
        *   `Rule::SingleLineImplicitStringConcatenation` and `Rule::MultiLineImplicitStringConcatenation` for `ISC_syntax_error.py`.
    *   In `crates/ruff_linter/src/rules/pycodestyle/mod.rs`, add:
        *   Five blank-lines rules for `E30_syntax_error.py`.
    *   In `crates/ruff_linter/src/rules/pylint/mod.rs`, add:
        *   `Rule::InvalidCharacterBackspace` for `invalid_characters_syntax_error.py`.

*   Create or update snapshot files:
    *   Create snapshots for each new test case in the appropriate directories.
    *   Update `COM81_syntax_error.py.snap` to reflect new diagnostics and line number shifts.

*   Update invalid character rules:
    *   Ensure `PLE2510` violations are reported for backspace characters inside properly-terminated strings, not inside unterminated strings.

*   Update implicit string concatenation rules:
    *   Report `ISC001` violations for consecutive complete string literals, avoiding false positives from unterminated strings.

*   Ensure blank line rules detect violations in `E30_syntax_error.py` despite syntax errors.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.