## Description

The lint rule that flags unnecessary dictionary spread operations generates broken auto-fixes when the dictionary being spread is wrapped in parentheses.

For example, when code spreads a parenthesized dictionary literal into another dictionary, the linter correctly identifies this as an unnecessary spread that could be simplified. However, the suggested fix incorrectly removes the inner dict braces while leaving behind orphaned closing parentheses, resulting in code that cannot be parsed at all.

## Expected Behavior

- When a dictionary literal is spread after being wrapped in parentheses, the lint rule should still flag it as an unnecessary spread.
- The auto-fix should remove the spread operator, all enclosing parentheses, and the outer dict braces, producing valid and equivalent Python code.
- Comments that appear between the spread operator and the dictionary content should be preserved in the output.
- The fix should handle one or more layers of wrapping parentheses correctly.

## Why This Matters

Applying the currently generated fix introduces a syntax error into the user's code, making the auto-fix feature actively harmful in this scenario. Developers who apply the suggested fix will break their codebase. The fix should either be correct or not offered at all.

## Reference

See issue: https://github.com/astral-sh/ruff/issues/15366
