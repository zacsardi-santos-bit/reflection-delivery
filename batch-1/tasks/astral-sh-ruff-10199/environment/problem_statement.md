## Description

The quote-style linter incorrectly offers automatic fixes in cases where applying the fix would corrupt the user's code. Specifically, when Python source code contains implicit string concatenation (adjacent string literals placed next to each other without an explicit concatenation operator), converting a string token to the preferred quote style can inadvertently create a triple-quote sequence at the boundary between two adjacent strings. Triple-quoted strings have a completely different meaning in Python and would introduce a syntax error.

For example, when the preferred style is double quotes and the source contains an empty single-quoted string immediately followed by a double-quoted string, converting the empty string would place two double quotes next to the opening double quote of the adjacent string, producing a triple-quote sequence.

## Expected Behavior

- When converting a string to the preferred quote style would create a triple-quote sequence at its boundary with an adjacent concatenated string, the linter should **still report** a style violation but **must not offer an auto-fix** for that specific token.
- This applies to both regular inline strings and to strings in docstring positions.
- Strings in the same implicit concatenation that can be safely converted should still receive auto-fixable diagnostics.
- The affected violations should reflect that a fix is only sometimes available (not always), since some tokens can be fixed and others cannot.

## Why This Matters

Applying the suggested auto-fix would introduce a Python syntax error, potentially breaking working code silently. Users relying on "fix all" workflows would end up with invalid files.
