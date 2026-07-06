## Description

The linter crashes with a fatal stack overflow when it encounters a type annotation that is a quoted string containing another nested string literal — for example, an annotation that wraps a string value inside another string. Instead of gracefully handling or rejecting this unusual pattern, the linter enters infinite recursion and aborts entirely.

## Expected Behavior

- The linter should process files containing quoted annotations that nest another string literal without crashing
- Such an annotation should be recognized as not resolvable to a concrete type and treated as a dynamically typed expression
- The linter should still report a missing return type annotation for functions that use this pattern
- The linter should report that the annotation represents a dynamically typed expression

## Steps to Reproduce

Define a function with a parameter whose annotation is a quoted string wrapping another quoted string (optionally with escape sequences in the inner string). Running the linter over that file causes a fatal crash rather than producing diagnostics.

## Why This Matters

Any codebase containing this annotation pattern causes the linter to crash entirely on that file, making it impossible to get any linting results. The linter should handle all syntactically valid Python gracefully, even if the annotation in question cannot be resolved to a meaningful type.
