## Description

The linter incorrectly reports false positives for undeclared variables inside Svelte template snippet blocks when the snippet's parameters use default values. Variables that are legitimately declared by a snippet's parameter list (including via all forms of destructuring patterns with defaults) are being reported as undeclared, even though they are perfectly valid bindings that should be in scope within the snippet body.

## Expected Behavior

- When a snippet parameter uses a plain default value, the parameter variable should be recognized as declared within the snippet.
- When a snippet parameter uses object destructuring with a default value, all destructured property variables should be recognized as declared.
- When a snippet parameter uses array destructuring with a default value, all destructured element variables should be recognized as declared.
- Nested destructuring patterns (objects inside arrays, objects inside objects, etc.) with default values should also have their bound variables recognized as declared.
- Rest patterns combined with defaults should have both the rest variable and any direct properties recognized as declared.
- Variables that are genuinely not declared (neither as snippet parameters nor in the outer component scope) should still be flagged correctly.

## Why This Matters

Developers writing Svelte components that use snippet blocks with default parameters receive spurious lint errors on valid code. This undermines trust in the linter and forces developers to suppress warnings that should not exist. The linter should only flag variables that are truly undeclared, not variables that are properly introduced through snippet parameter declarations.
