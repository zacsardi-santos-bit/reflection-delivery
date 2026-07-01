## Description

The lint rule that detects and auto-fixes unnecessary constructor calls around native literals has a bug when the argument is a number with a unary sign operator (positive or negative). In several expression contexts, removing the constructor call produces code that is either syntactically invalid or subtly changes the meaning of the expression.

For example, if a signed numeric literal ends up directly to the left of an exponentiation operator, or is used as the object of an attribute access, as the callee of a call, or as the target of a subscript, the fix currently generates broken or wrong code. Similarly, when the fix is applied inside an async-await expression, the result can be a syntax error.

## Expected Behavior

- When removing a constructor call wrapping a signed literal would produce an ambiguous or invalid expression due to operator precedence or context, the replacement should be wrapped in parentheses to preserve the original semantics and ensure syntactic validity.
- This applies to signed literals appearing in contexts such as: the operand of a binary operator, the object of subscript access, the callee of a call, and inside an async-await expression.
- Even when the signed literal is on the right-hand side of an operator (inside a subscript or call argument), parentheses should still be added.
- When the constructor call contains inline comments (e.g., between the opening parenthesis and the argument), the auto-fix should be marked as unsafe, since the fix would drop those comments.

## Why This Matters

Auto-fixes that produce invalid syntax, change program semantics, or silently discard comments are bugs that erode trust in the linter's fix functionality. Users who apply fixes automatically (e.g., via editor integration or CI) could end up with broken code.
