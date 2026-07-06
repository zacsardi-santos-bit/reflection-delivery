Update the RUF057 lint rule to prevent false positives when rounding integer values with specific precision arguments. Ensure the rule only flags truly unnecessary rounding operations.

*   Modify the RUF057 lint rule in `crates/ruff_linter/src/rules/ruff/rules/unnecessary_round.rs`:
    *   Do not flag `round()` calls on integer values when the `ndigits` argument is a negative integer literal.
    *   Do not flag `round()` calls on integer values when the `ndigits` argument is a variable or an arithmetic expression.
    *   Continue to flag `round()` calls on integer values when the `ndigits` argument is absent, explicitly `None`, or a non-negative integer literal.

*   Update the fixture file `crates/ruff_linter/resources/test/fixtures/ruff/RUF057.py`:
    *   Mark cases with negative literal `ndigits` (e.g., `-2`) as 'No error'.
    *   Mark cases with non-literal or expression `ndigits` (e.g., inferred int, `3 + 4`, unknown variable) as 'No error' when the first argument is an integer.
    *   Ensure cases with absent, `None`, or non-negative integer literal `ndigits` remain marked as errors.

*   Regenerate or manually update the snapshot file `crates/ruff_linter/src/rules/ruff/snapshots/ruff_linter__rules__ruff__tests__preview__RUF057_RUF057.py.snap`:
    *   Ensure it reflects the updated rule behavior with no diagnostics for calls with negative, variable, or expression `ndigits`.
    *   Maintain diagnostics for calls with absent, `None`, or non-negative integer literal `ndigits`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.