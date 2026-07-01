## Description

The engine's binary data viewing infrastructure is incomplete in two ways. First, each typed array's underlying binary type has no knowledge of which JavaScript prototype it corresponds to. Second, the clamped byte typed array variant is entirely missing from the system.

Without a prototype reference on each binary type, the engine cannot properly set up typed array prototype chains at runtime. This causes crashes across a large range of typed array operations — construction, property access, iteration, sorting, filtering, slicing, and more.

## Expected Behavior

- Each binary type used in typed array views should be associated with its corresponding JavaScript typed array prototype (for example, the unsigned 8-bit integer type links to its 8-bit unsigned integer array prototype, and the 64-bit floating-point type links to its 64-bit float array prototype).
- A clamped unsigned 8-bit type should exist and be fully integrated as a viewable binary type, linked to the appropriate prototype, and using clamped conversion semantics when writing values.
- After these additions, typed array construction, prototype method calls, and subclassing should no longer crash on standard conformance tests.

## Why This Matters

Typed arrays are a core part of the JavaScript specification and are heavily used in real-world code. The current crash behavior on a large portion of conformance tests indicates a foundational gap in the engine's typed array support. Fixing the prototype wiring and adding the missing clamped type will unblock hundreds of currently-crashing tests and establish the correct architectural foundation for typed array operations.
