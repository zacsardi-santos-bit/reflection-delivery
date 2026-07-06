## Description

The linter rule that detects and automatically removes trivial finally clauses produces incorrect output when the body of the try block contains a line that starts with a form-feed (page-break) control character.

When the auto-fix is applied to such a block, the resulting code is syntactically invalid: the line that started with the form-feed character retains its original indentation instead of being dedented to the correct level. This causes the fixed file to fail Python's parser.

## Expected Behavior

- The auto-fix should correctly remove the try-finally wrapper and extract the body with proper indentation, even when one or more lines in the body start with a form-feed character.
- Form-feed characters at the start of a line should be preserved in the output, but the indentation that was introduced by the try block should be removed — consistent with how Python's own lexer treats form-feed characters as resetting the indentation context.
- The resulting code must be syntactically valid Python.

## Why This Matters

Python source files occasionally use form-feed characters as page separators (they appear in some codebases as a visual separator between sections). The linter should handle these files gracefully and not generate broken code when applying automatic fixes.
