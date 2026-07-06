I'm hitting a crash in the linter when it processes a file that contains a type annotation written as a quoted string wrapping another nested string literal (with an escape sequence inside the inner string).

*   When the linter processes a Python function whose parameter annotation is a quoted string containing a nested string literal (with or without escape sequences), it must complete analysis without causing a stack overflow or fatal crash.

*   A quoted annotation that resolves to another string literal (a string-within-a-string) must be treated as a dynamically typed expression rather than a valid forward reference, preventing infinite recursion during type resolution.

*   The linter must emit an ANN201 diagnostic ('Missing return type annotation for public function `quoted_escape`') for a public function that uses a string-within-a-string annotation and has no return type annotation, with a suggested fix adding `-> None`.

*   The linter must emit an ANN401 diagnostic ('Dynamically typed expressions (typing.Any) are disallowed in `x`') for a parameter whose annotation is a quoted string containing a nested string literal, since such an annotation resolves to Any.

*   The snapshot file at `crates/ruff_linter/src/rules/flake8_annotations/snapshots/ruff_linter__rules__flake8_annotations__tests__defaults.snap` must be updated to include the ANN201 and ANN401 diagnostics for the new `quoted_escape` fixture function at line 172 of `annotation_presence.py`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.