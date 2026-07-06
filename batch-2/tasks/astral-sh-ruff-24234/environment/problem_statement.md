# Fix autofix for multiline conditional expressions

## Description

The lint rule that detects unnecessary empty conditional statements (where the condition has side effects but the body does nothing) can apply an automated fix to replace the entire statement with the condition as a standalone expression. This works correctly for single-line conditions, but the fix produces **invalid Python code** when the condition spans multiple lines.

## Cases affected

Two distinct multiline patterns are broken:

1. **Multiline binary/arithmetic expression wrapped in outer parentheses** — when the condition is a binary expression spanning multiple lines and enclosed in outer parentheses, removing the keyword and colon while leaving the inner content produces invalid syntax because the expression needs those outer parentheses to remain a valid statement.

2. **Multiline function call** — when the condition is a function call with arguments on separate lines, the fix must correctly produce the call as a standalone expression statement without adding unnecessary extra wrapping.

## Expected Behavior

- For a multiline condition already enclosed in outer parentheses, the fix should preserve the parenthesized form when converting to a standalone statement.
- For a multiline function call whose line breaks are all within the call's argument list, the fix should emit the call directly as a standalone expression statement without additional parentheses.

## Why This Matters

Writing multiline conditions in conditional statements is common for readability. When this rule fires and the user applies the suggested fix, they should end up with valid, correctly formatted Python — not a syntax error.
