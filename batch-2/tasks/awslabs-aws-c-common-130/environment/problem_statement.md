## Description

The byte buffer and encoding APIs in this library have grown inconsistently over time. Several groups of related functions have argument orderings that differ from each other and from the natural convention of "destination first." Additionally, some functions are named after the wrong data type — write operations that modify mutable buffers carry the name of the read-only cursor type, and splitting functions that only need to read data accept a mutable buffer type instead of a read-only cursor. This makes the API harder to learn and use correctly.

## Expected Behavior

- Buffer initialization functions should place the output buffer pointer as the first argument, followed by the allocator and capacity, matching the conventional "output first" pattern.
- Write operations (for bytes, integers, whole buffers, and strings) should be associated with the mutable buffer type, not the read-only cursor type, in both name and parameter type.
- Encoding and decoding functions should accept the read-only cursor type for their input parameter, since they do not need to mutate the input.
- String splitting functions should accept the read-only cursor type, since they do not mutate the input string.
- The limited-split variant of the split function should place the split count before the output list parameter.
- Integer serialization functions should take the value as the first argument and the destination buffer as the second, matching the natural "what to write" before "where to write it" pattern.
- A new utility that creates a write-ready buffer over a pre-existing array (with length set to zero) should be provided, separate from the existing utility that creates a buffer over an array that is treated as already containing data.

## Why This Matters

Inconsistent argument ordering causes bugs when callers accidentally swap arguments of the same type. Naming write functions after the read-only type causes confusion about which type should be used and can lead to incorrect usage. Fixing these API inconsistencies now, before the library is widely deployed, prevents long-term maintenance burden and makes the API safer and more ergonomic for all consumers.
