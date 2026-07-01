Implement a fix for the Python linter rule that correctly handles unnecessary dictionary spread operations when the dictionary is wrapped in parentheses. Ensure the auto-fix removes all unnecessary tokens and preserves comments, resulting in valid Python code.

*   Update the unnecessary-spread lint rule to:
    *   Detect spread operators applied to parenthesized dictionary literals.
    *   Flag these as violations similarly to unparenthesized cases.

*   Implement the auto-fix to:
    *   Remove the double-star operator token.
    *   Remove all opening parentheses between the double-star and the opening brace of the dictionary.
    *   Remove the opening and closing braces of the dictionary.
    *   Remove all corresponding closing parentheses.
    *   Preserve comments between the double-star operator and the dictionary content.
    *   Handle multiple layers of parentheses, removing all of them.
    *   Address edge cases with closing braces and parentheses on separate lines, trailing commas, and comments.

*   Ensure the snapshot file at `crates/ruff_linter/src/rules/flake8_pie/snapshots/ruff_linter__rules__flake8_pie__tests__PIE800_PIE800.py.snap` is updated to:
    *   Include the correct expected diagnostic output and fix diffs for all new test cases.

*   Verify that applying the auto-fix to any new test cases does not produce syntax errors in the source file.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.