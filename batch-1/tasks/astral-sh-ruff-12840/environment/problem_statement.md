## Description

There are two related issues with the linter rules that detect unnecessary else/elif blocks following early exit statements (return, raise, continue, break):

1. **Crash on backslash line continuation**: When the linter encounters code where an else block's body begins with a backslash line continuation character (a valid but uncommon Python syntax), it crashes with an arithmetic overflow error instead of reporting the diagnostic. This prevents linting any codebase that contains such patterns.

2. **Auto-fix hidden behind preview mode**: The automatic correction capability for this family of rules has been gated behind an experimental preview mode flag. This means that in the default configuration, the linter detects these unnecessary else/elif blocks but cannot offer to fix them automatically — users must opt into experimental features to get the fix.

## Expected Behavior

- When the linter encounters an unnecessary else block whose body starts with a backslash line continuation, it should report the warning gracefully without crashing. Because the backslash continuation makes automatic reformatting unreliable, the auto-fix should be skipped for this specific case.
- The auto-fix for all four related rules (unnecessary else/elif after return, raise, continue, and break) should be available by default, without requiring preview mode.

## Why This Matters

Any project with backslash line continuations at the start of an else body cannot currently be linted without the tool crashing. Additionally, keeping the auto-fix behind a preview flag means the vast majority of users miss out on a useful code-quality improvement that is ready for general availability.
