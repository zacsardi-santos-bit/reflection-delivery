Implement a fix in the linter to handle trailing commas in Python collection definitions correctly, ensuring that the auto-fix does not produce invalid syntax. Update the relevant snapshot files to reflect the new expected outputs for these cases.

*   Update the `MultilineStringSequenceValue` struct in `sequence_sorting.rs`:
    *   Ensure a trailing comma is appended to the last sorted item only if:
        *   The original sequence had a trailing comma (`ends_with_trailing_comma` is true).
        *   The postlude text does not already start with a comma.
    *   Use the `first_non_trivia_token` function and `SimpleTokenKind` enum from the `ruff_python_trivia` crate to inspect the postlude text.

*   Ensure the unsorted `__all__` rule:
    *   Detects and offers a valid auto-fix for multiline `__all__` definitions with trailing commas directly before the closing bracket on the same line.
    *   Handles trailing commas with extra surrounding whitespace before the closing bracket.
    *   Manages trailing commas on their own line, possibly surrounded by blank lines and comments.
    *   Deals with commas on their own lines with comments interleaved between elements.

*   Ensure the unsorted `__slots__` rule:
    *   Detects and offers a valid auto-fix for multiline `__slots__` definitions with the same trailing-comma edge cases as `__all__`.

*   Update snapshot files:
    *   Modify `RUF022_RUF022.py.snap` to include expected diagnostic output for new trailing-comma cases in `RUF022.py`.
    *   Modify `RUF023_RUF023.py.snap` to include expected diagnostic output for new trailing-comma cases in `RUF023.py` and update existing snapshot strings as necessary.

*   Ensure all auto-fixes result in syntactically valid Python code, verified by the test framework.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.