Resolve the issues with the linter rules that detect unnecessary else/elif blocks following early exit statements. Ensure the linter does not crash on encountering backslash line continuations and make the auto-fix feature available by default without requiring preview mode.

*   Update the linter to handle unnecessary else blocks after return statements (RET505):
    *   Ensure it reports a diagnostic when an else block follows a return statement, even if the else body starts with a backslash line continuation character.
    *   Use the diagnostic message: 'Unnecessary `else` after `return` statement' with help text 'Remove unnecessary `else`'.
    *   Do not attach an automatic fix to this diagnostic when a backslash line continuation is involved.

*   Prevent the linter from crashing:
    *   Ensure the linter does not panic or crash when processing code where an else block's body begins with a backslash line continuation character.
    *   Address the arithmetic overflow issue in the indentation adjustment logic that previously caused crashes during auto-fix attempts.

*   Enable auto-fix by default:
    *   Make the auto-fix for all four related rules (RET505 for return, RET506 for raise, RET507 for continue, RET508 for break) available without requiring preview mode.
    *   Ensure existing diagnostics for these rules include a '[*]' safe-fix marker and an 'ℹ Safe fix' block showing the corrective edit.

*   Update test infrastructure:
    *   Modify snapshot files for all eight flake8_return rules (RET501–RET508) to reflect:
        *   '[*]' safe-fix markers on all existing RET505–RET508 diagnostics.
        *   'ℹ Safe fix' diff blocks for those fixes.
        *   The new RET505 diagnostic for the backslash continuation case without any fix marker.
    *   Remove the separate preview-mode test function for RET505–RET508; integrate these rules into the standard test suite.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.