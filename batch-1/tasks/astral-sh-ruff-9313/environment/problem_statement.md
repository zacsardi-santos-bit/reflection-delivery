# Add lint rule for overly specific return type annotations on iterator methods

## Description

When implementing the iterator or async iterator protocols in Python, the iteration methods only need to declare that they return an iterator type. However, many developers annotate these methods as returning a more specific generator type, even when the generator's send and return type parameters are trivially unused (set to empty placeholder values indicating "none" or "any"). This is unnecessarily complex — a more idiomatic and simpler annotation would use the iterator type directly.

Currently, ruff has no rule to detect this pattern in type stubs or annotated Python code.

## Expected Behavior

A new lint rule should be added that detects when:
- An iteration method in a class declares it returns a generator type when a simpler iterator type would be sufficient (i.e., the generator's send type and return type are trivially unused placeholder values)
- An async iteration method in a class declares it returns an async generator type when a simpler async iterator type would suffice (i.e., the send type is a trivially unused placeholder value)

The rule should suggest using the simpler iterator type instead.

## Scope and Edge Cases

The rule should only apply to methods in class bodies — module-level functions with the same names should be ignored. It should also skip:
- Iterator methods that return generator types with meaningful send or return type parameters
- Async-defined iteration methods
- Iteration methods with unusual or extra parameters
- In regular (non-stub) Python files, iteration methods with non-trivial generator bodies (e.g., those that return a meaningful value, receive values from sends, or have complex body logic)

## Why This Matters

This type of overly specific annotation adds noise to type stubs and annotated code. The simpler iterator type annotation is semantically equivalent and more idiomatic for classes that only need to declare compliance with the iterator protocol.
