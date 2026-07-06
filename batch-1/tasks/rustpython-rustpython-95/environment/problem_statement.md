## Description

RustPython currently double-evaluates the truthiness of operands in compound boolean expressions when those expressions are used as conditions in certain statements. Python's semantics guarantee that each operand in a boolean chain is checked for its truth value at most once — this is part of what makes short-circuit evaluation safe for expressions with side effects.

For example, consider an object with a custom truth-check method that records whether it has already been checked and raises an error the second time. Under correct Python semantics, using such an object in a compound "or" or "and" condition inside an "if" statement, "while" loop, or "assert" should never trigger the error — because each object's truth value is only looked up once.

## Expected Behavior

- In a chain of "or" conditions inside an "if" statement, a truthy operand causes an immediate jump into the body without re-checking the operand's boolean value.
- In a chain of "and" conditions inside an "if" statement, a falsy operand causes an immediate jump past the body without re-checking the operand's boolean value.
- The same single-evaluation guarantee applies in "while" loop conditions and "assert" statements.
- Objects with side-effecting truth-check methods must not have those methods called more than once per use-site.

## Why This Matters

Python code that relies on the fact that each operand is checked at most once — including objects with side effects in their truth-check methods — may behave incorrectly or crash when run under RustPython. Fixing this makes RustPython's boolean evaluation semantics fully compatible with CPython.
