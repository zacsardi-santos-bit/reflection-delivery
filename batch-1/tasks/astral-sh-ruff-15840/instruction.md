Update the linter to handle comments in type alias declarations correctly. Ensure that comments are preserved or the fix is marked as unsafe when necessary, based on their location in the code.

*   Classify the automatic fix as 'Unsafe' if:
    *   A comment exists between the start of a type-alias-type assignment and the start of the type value expression.
    *   The fix text should not include this comment.

*   Classify the automatic fix as 'Safe' if:
    *   A comment appears after the closing parenthesis on the same line, outside the statement's AST node range.
    *   A comment is inside the type value expression itself; preserve it verbatim in the replacement text.
    *   A TypeAlias annotation in a type stub file has a multiline value expression with inline comments; preserve these comments verbatim.

*   Ensure that for type aliases with no type parameters, the generated replacement does not include empty square brackets. Produce 'type Name = value' instead of 'type Name[] = value'.

*   Update snapshot files:
    *   Modify `crates/ruff_linter/src/rules/pyupgrade/snapshots/ruff_linter__rules__pyupgrade__tests__UP040.py.snap` to include expected diagnostic output for three new test cases in UP040.py, with correct 'Unsafe fix' vs 'Safe fix' classification and exact replacement text.
    *   Modify `crates/ruff_linter/src/rules/pyupgrade/snapshots/ruff_linter__rules__pyupgrade__tests__UP040.pyi.snap` to include expected diagnostic output for a new test case in UP040.pyi, showing a 'Safe fix' that preserves inline comments inside the value expression.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.