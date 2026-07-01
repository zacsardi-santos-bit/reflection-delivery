## Description

The built-in debugging output function currently accepts any value regardless of its type, even when the type cannot be meaningfully serialized for debugging. Passing a value of an unsupported custom type (such as an internal string builder primitive) to the debugging function silently succeeds at compile time but causes issues at runtime or during code generation.

There should be a compile-time type check that immediately tells developers when they have passed an unsupported type, rather than allowing it through silently.

## Expected Behavior

- The debugging output function should succeed (no compile error) when called with values of all standard types: integers, booleans, strings, addresses, cells, builders, slices, null values, void returns, optional types, and map types.
- The debugging output function should produce a clear compile-time error when called with a value of an unsupported type (such as a custom or non-standard primitive). The error message should name the offending type and point to the relevant documentation.

## Why This Matters

Without this type check, developers who accidentally pass an unsupported type to the debugging function receive no feedback at compile time. Adding a type guard here gives developers an immediate, actionable error at the earliest possible stage of the development cycle, preventing confusing failures that would otherwise surface much later.
