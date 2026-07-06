I'm working with a lint rule that flags unnecessary empty conditional statements — where the condition has side effects but the body does nothing.

*   The rule that flags unnecessary empty `if` statements must be extended to correctly handle multiline expressions as conditions — specifically when the condition contains a line break at the top level (i.e., not enclosed within any inner parentheses, brackets, or braces).

*   When a multiline condition is already surrounded by outer parentheses in the source (such as a binary expression wrapped in `( ... )`), the generated fix must use the parenthesized source range as the replacement expression statement — preserving the outer parens so the expression remains syntactically valid as a standalone statement.

*   When a multiline function call is used as the condition and its internal line breaks are fully enclosed within the call's own parentheses (no top-level line breaks), the fix must use the raw condition text directly as the replacement statement without adding any additional outer parentheses.

*   The snapshot file at `crates/ruff_linter/src/rules/ruff/snapshots/ruff_linter__rules__ruff__tests__RUF050_RUF050.py.snap` must be updated to include the expected diagnostic entries and fix diffs for the two new multiline test cases. Because the test environment sets `INSTA_UPDATE=no`, the snapshot file must be updated manually alongside the rule implementation fix.

*   The rule implementation is located at `crates/ruff_linter/src/rules/ruff/rules/unnecessary_if.rs` and the fix-generation logic within it must be updated to handle multiline conditions.


*   Interface details: NO INTERFACES NEEDED

The test runs the full linting pipeline on the fixture file `crates/ruff_linter/resources/test/fixtures/ruff/RUF050.py` and compares the diagnostic output against a snapshot. No specific new public functions or classes are called by name from the test. The implementation changes needed are internal to:

- `crates/ruff_linter/src/rules/ruff/rules/unnecessary_if.rs` — where the fix generation logic must be updated
- `crates/ruff_linter/src/rules/ruff/snapshots/ruff_linter__rules__ruff__tests__RUF050_RUF050.py.snap` — where the snapshot must be updated to include the expected diagnostic output for the two new multiline test cases


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.