Fix the linter rule RUF027 to correctly detect and suppress warnings for string literals that appear to be missing an interpolation prefix. Address false negatives and false positives as specified, and reorganize test fixtures accordingly.

*   Update the lint rule for detecting missing interpolation:
    *   Ensure strings passed to methods accessed through object attributes are flagged.
    *   Suppress warnings for strings with grouped concatenation followed by explicit formatting.
    *   Suppress warnings for strings with multiple chained attribute method calls.
    *   Suppress warnings for strings passed with positional arguments matching format variables.

*   Modify the `should_be_fstring` function in `crates/ruff_linter/src/rules/ruff/rules/missing_fstring_syntax.rs`:
    *   Do not return false early for attribute access unless the receiver is the string itself or the previous expression.
    *   Collect positional argument names in addition to keyword argument names.
    *   Track the last-seen expression to suppress warnings for chained attribute calls.

*   Update test registrations in `crates/ruff_linter/src/rules/ruff/mod.rs`:
    *   Remove the existing entry for `RUF027.py`.
    *   Add entries for `RUF027_0.py` and `RUF027_1.py`.

*   Reorganize test fixtures:
    *   Create `RUF027_0.py` in `crates/ruff_linter/resources/test/fixtures/ruff/` for positive cases.
        *   Include all previous positive cases and a new `method_calls()` function.
    *   Create `RUF027_1.py` in `crates/ruff_linter/resources/test/fixtures/ruff/` for negative cases.
        *   Include all previous negative cases and three new cases: 
            *   `print(("{a}" "{c}").format(a=1, c=2))`
            *   `print("{a}".attribute.chaining.call(a=2))`
            *   `print("{a} {c}".format(a))`
    *   Remove the old `RUF027.py` fixture.

*   Update snapshot files:
    *   Create `ruff_linter__rules__ruff__tests__RUF027_RUF027_0.py.snap` for positive cases.
        *   Include diagnostics for all positive cases in `RUF027_0.py`, including the new `method_calls()` entry.
    *   Create `ruff_linter__rules__ruff__tests__RUF027_RUF027_1.py.snap` for negative cases.
        *   Ensure it contains no diagnostic entries, confirming no false positives.
    *   Delete the old `ruff_linter__rules__ruff__tests__RUF027_RUF027.py.snap`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.