Implement a lint rule to correctly handle unnecessary integer conversions in multiline function calls. Ensure the fix maintains the original semantics of the code and correctly identifies unsafe fixes when comments are present.

*   Update the rule implementation in `unnecessary_cast_to_int.rs`:
    *   Modify the `has_own_parentheses` function to:
        *   Accept `comment_ranges: &CommentRanges` and `source: &str` parameters.
        *   Check if the function name and opening parenthesis of arguments are on the same line using `lines_after_ignoring_trivia`.
        *   Return `false` if they are on different lines, indicating the need for wrapping parentheses.
    *   Extend the `unwrap_int_expression` function to:
        *   Check for comments between the start of the outer call and the start of the argument, and between the end of the argument and the end of the outer call.
        *   Downgrade fix applicability to `Applicability::Unsafe` if any comment range intersects these text ranges.

*   Update the snapshot file `ruff_linter__rules__ruff__tests__preview__RUF046_RUF046.py.snap`:
    *   Include expected diagnostic messages, fix safety classifications, and code transformations for nine new test cases:
        1. Transform `int(round\n(1))` to `(round\n(1))` with a safe fix.
        2. Transform `int(round # a comment\n# and another comment\n(10)\n)` to `(round # a comment\n# and another comment\n(10))` with a safe fix.
        3. Transform `int(round (17))` to `round (17)` with a safe fix.
        4. Transform `int( round (\n    17\n))` to `round (\n    17\n)` with a safe fix.
        5. Transform `int((round)  # Comment\n(42)\n)` to `((round)  # Comment\n(42))` with a safe fix.
        6. Transform `int((round  # Comment\n)(42)\n)` to `(round  # Comment\n)(42)` with a safe fix.
        7. Mark `int(  # Unsafe fix because of this comment\n...\n)` as an unsafe fix.
        8. Mark `int(\n    round(\n        42\n    ) # unsafe fix because of this comment\n)` as an unsafe fix.
        9. Mark `int(\n    round(\n        42\n    ) \n# unsafe fix because of this comment\n)` as an unsafe fix.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.