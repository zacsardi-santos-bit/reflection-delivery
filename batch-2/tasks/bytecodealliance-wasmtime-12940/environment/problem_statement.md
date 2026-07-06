## Description

The Winch compiler backend (the single-pass baseline compiler) does not currently support basic reference type instructions on x86_64. This means any WebAssembly module that uses function references, null references, typed conditional selection over references, or reference-typed table operations cannot be compiled and executed with Winch. These reference type primitives are a fundamental part of the WebAssembly reference types proposal and are needed for many real-world modules.

## Expected Behavior

- Creating a null function reference should produce a zero/null value.
- Testing whether a reference is null should return 1 (true) for null references and 0 (false) for non-null references.
- Obtaining a function reference for a declared function should work correctly.
- Selecting between two function references conditionally should pick the correct one based on the condition.
- Table operations involving function references (reading, writing, growing, and checking table size) should all work correctly under Winch.
- Calling through a table slot populated with a function reference should invoke the correct function.
- Trapping with appropriate messages on uninitialized table element access and out-of-bounds table access should work as expected.

## Why This Matters

Without reference type support in Winch, any module using these instructions must either be rejected or fall back to a different compilation tier. Adding support enables the Winch baseline compiler to handle these common WebAssembly patterns and run the full reference types test suite. Additionally, an existing test that was unnecessarily requiring the garbage-collection types feature enabled should be corrected to only require the reference types feature.
