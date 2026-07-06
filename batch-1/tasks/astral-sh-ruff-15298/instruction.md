Update the implicit return lint rule to correctly handle functions that terminate by calling another function annotated as "never returning," regardless of whether the annotation comes from the standard library or a type extensions library. Ensure that the rule does not produce false-positive diagnostics for such cases and update the associated test fixtures and snapshots accordingly.

*   Modify the `is_noreturn_func` function in `crates/ruff_linter/src/rules/flake8_return/rules/function.rs`:
    *   Extend the function to recognize both `typing.NoReturn` and `typing_extensions.Never` annotations.
    *   Use `semantic.match_typing_qualified_name(&qualified_name, "Never")` to check for `typing_extensions.Never`.

*   Update the fixture file `crates/ruff_linter/resources/test/fixtures/flake8_return/RET503.py`:
    *   Replace nested NoReturn function cases with top-level functions `bar_no_return_annotation()` and `bar_never_annotation()`.
    *   Add parallel test cases for `typing_extensions.Never`.
    *   Document the known limitation regarding nested functions annotated with 'NoReturn' or 'Never'.

*   Update the snapshot files for the RET503 rule:
    *   Ensure the snapshots reflect the updated fixture, removing false-positive diagnostics for nested NoReturn functions.
    *   Retain diagnostics for the `f()` function case and the documented nested-function limitation.
    *   Update the following snapshot files:
        *   `ruff_linter__rules__flake8_return__tests__RET503_RET503.py.snap`
        *   `ruff_linter__rules__flake8_return__tests__preview__RET503_RET503.py.snap`

*   Ensure all snapshot tests for the flake8_return rule family (RET501–RET508) pass:
    *   Regenerate snapshot files by running the test suite in snapshot-update mode if necessary.
    *   Verify that snapshot files for unchanged rules (RET501, RET502, RET504–RET508) exist and are consistent with their fixtures.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.