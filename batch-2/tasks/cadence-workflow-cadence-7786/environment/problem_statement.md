## Description

The type mapper fuzz tests for schedule-related types are verbose and repetitive. Every test manually sets up its own fuzzer with the same boilerplate: custom time and enum fuzzers, iteration logic, nil/empty/filled classification, and round-trip assertions. This makes the tests harder to maintain and inconsistent across files.

We need a shared, reusable test utility for mapper round-trip fuzz testing that encapsulates all this boilerplate. The utility should allow a test to express a round-trip check in just a few lines.

## Known Bugs to Fix

The existing field-clearing logic (which zeroes out protobuf-internal metadata fields before comparing values) has several bugs that must be addressed:

1. **Slice elements are not modified in place**: When clearing fields in slices of value-type structs, the current code operates on copies of elements rather than the originals, so the clearing has no effect.

2. **Pointer indirection is not handled correctly**: When the input to the field-clearing function is a pointer-to-pointer (which is the natural usage when holding a variable reference to a pointer type), the function silently does nothing rather than dereferencing through to the underlying struct.

3. **Overly aggressive field clearing**: A field named "State" was previously always cleared, even when it is a legitimate business logic field. Only fields with protobuf-internal naming conventions or that are explicitly listed by the caller should be cleared.

## Expected Behavior

- A single utility function enables concise round-trip fuzz testing across all mapper types
- The field-clearing utility correctly handles nested structs, slices of value-type structs, slices of pointer-type structs, and multiple levels of pointer indirection
- Legitimate fields (e.g., named "State") are preserved unless explicitly listed for exclusion
- Protobuf-internal fields (those following the standard internal naming prefix) are always cleared

## Why This Matters

Fixing these bugs ensures that round-trip tests give correct results and don't silently pass due to fields being left uncleared when they should be zeroed out.
