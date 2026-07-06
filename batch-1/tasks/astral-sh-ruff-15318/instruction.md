Update the linter to correctly handle regex patterns containing unescaped closing parentheses. Ensure that both the "unnecessary regular expression" and "ambiguous test assertion pattern" rules recognize the closing parenthesis as a regex metacharacter.

*   Implement the function `char_is_regex_metacharacter` in `crates/ruff_linter/src/rules/ruff/rules/pytest_raises_ambiguous_pattern.rs`:
    *   Ensure it returns `true` for the closing parenthesis `')'` and other existing metacharacters: `'.', '^', '$', '*', '+', '?', '{', '[', '\\', '|', '('`.
*   Update the `unnecessary_regular_expression` rule in `crates/ruff_linter/src/rules/ruff/rules/unnecessary_regular_expression.rs`:
    *   Include `')'` in the inline `has_metacharacters` check.
    *   Ensure that if a pattern contains any metacharacter, including `')'`, the rule returns early without emitting a diagnostic.
*   Modify the snapshot file `crates/ruff_linter/src/rules/ruff/snapshots/ruff_linter__rules__ruff__tests__preview__RUF043_RUF043.py.snap`:
    *   Add a new diagnostic entry for the pattern `match="foo)"` at line 22.
    *   Include the message: "Pattern passed to `match=` contains metacharacters but is neither escaped nor raw".
    *   Provide help text: "Use a raw string or `re.escape()` to make the intention explicit".
    *   Increment all subsequent line numbers by 3.
*   Adjust the snapshot file `crates/ruff_linter/src/rules/ruff/snapshots/ruff_linter__rules__ruff__tests__preview__RUF055_RUF055_0.py.snap`:
    *   Ensure no diagnostic is produced for `re.sub(r"a)bc", "", s)`.
    *   Increment all existing diagnostic line numbers by 1 to account for the new line in the fixture file.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.