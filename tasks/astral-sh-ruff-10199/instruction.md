Implement a fix for the quote-style linter to prevent it from offering automatic fixes that would corrupt Python code by creating triple-quote sequences. Ensure the linter still reports style violations but does not offer auto-fixes when such fixes would result in syntax errors.

*   Update the linter logic to handle cases where converting a string to the preferred quote style would create a triple-quote sequence:
    *   For inline string tokens (Q000 context):
        *   Emit a `BadQuotesInlineString` diagnostic without an auto-fix if the token is empty and the character immediately following it is the preferred quote character.
        *   Emit a `BadQuotesInlineString` diagnostic without an auto-fix if the token is immediately preceded by two consecutive preferred-style quote characters.
    *   For docstring-position string tokens (Q002 context):
        *   Emit a `BadQuotesDocstring` diagnostic without an auto-fix if the token is empty and the character immediately following it is the preferred quote character.
*   Change the fix availability for `BadQuotesInlineString` and `BadQuotesDocstring` violations from `AlwaysFixable` to `Sometimes`, reflecting that some occurrences cannot be safely auto-fixed.
*   Implement logic to detect the 'would become triple quotes' condition:
    *   Check if the current token is an empty string and the following character is the preferred quote character.
    *   Check if the two characters immediately before the current token are both the preferred quote character.
*   Register new test cases in `crates/ruff_linter/src/rules/flake8_quotes/mod.rs`:
    *   Add `doubles_would_be_triple_quotes.py` to the `require_singles` test function.
    *   Add `singles_would_be_triple_quotes.py` to the `require_doubles` test function.
    *   Add mixed-quote docstring fixtures to `require_docstring_doubles` and `require_docstring_singles` test functions.
*   Create snapshot files in `crates/ruff_linter/src/rules/flake8_quotes/snapshots/` for each new test case:
    *   Use the naming convention: `ruff_linter__rules__flake8_quotes__tests__<snapshot_key>.snap`.
    *   Ensure snapshot content reflects the diagnostic output format, with or without the `[*]` fix marker as appropriate.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.