Implement a fix for the linter's auto-fix feature to correctly handle unnecessary constructor calls around signed numeric literals. Ensure that replacements are syntactically valid and preserve the original semantics, especially in contexts where operator precedence is a concern. Mark fixes as unsafe if they would remove inline comments.

*   Update the `native_literals` function in `crates/ruff_linter/src/rules/pyupgrade/rules/native_literals.rs`:
    *   Ensure that constructor calls wrapping signed literals are replaced with parenthesized literals when necessary.
    *   Mark fixes as unsafe if the constructor call contains inline comments.

*   Modify the `OperatorPrecedence` enum:
    *   Change its visibility to `pub(crate)` in `crates/ruff_linter/src/rules/pylint/rules/unnecessary_dunder_call.rs`.

*   Update the snapshot file `crates/ruff_linter/src/rules/pyupgrade/snapshots/ruff_linter__rules__pyupgrade__tests__UP018.py.snap`:
    *   Add entries for new test cases reflecting correct replacements and Safe/Unsafe labels.
    *   Ensure entries include:
        *   `int(-1) ** 0` → `(-1) ** 0` (Safe fix)
        *   `2 ** int(-1)` → `2 ** (-1)` (Safe fix)
        *   `int(-1)[0]` → `(-1)[0]` (Safe fix)
        *   `2[int(-1)]` → `2[(-1)]` (Safe fix)
        *   `int(-1)(0)` → `(-1)(0)` (Safe fix)
        *   `2(int(-1))` → `2((-1))` (Safe fix)
        *   `float(-1.0).foo` → `(-1.0).foo` (Safe fix)
        *   `await int(-1)` → `await (-1)` (Safe fix)
        *   `int(+1) ** 0` → `(+1) ** 0` (Safe fix)
        *   `float(+1.0)()` → `(+1.0)()` (Safe fix)
        *   `str(\n    '''Lorem\n    ipsum'''  # Comment\n).foo` → `'''Lorem\n    ipsum'''.foo` (Unsafe fix — contains inline comment)

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.