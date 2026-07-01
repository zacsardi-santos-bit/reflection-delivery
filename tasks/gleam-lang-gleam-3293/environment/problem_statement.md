## Description

When compiling Gleam programs to JavaScript, bit array expressions and patterns that use endianness or signedness qualifiers don't work correctly. Specifically, the `little` and `big` endianness options and the `signed`/`unsigned` options for integers, as well as sized floats (32-bit and 64-bit), are either silently ignored or produce incorrect output when targeting JavaScript.

## Expected Behavior

- Constructing a bit array with a little-endian integer segment should produce bytes in little-endian order (least significant byte first).
- Constructing a bit array with a big-endian integer segment should produce bytes in big-endian order (most significant byte first).
- Constructing a bit array with a 32-bit float segment should produce the correct 4-byte IEEE 754 single-precision representation.
- Constructing a bit array with a 64-bit little-endian float segment should produce the correct little-endian byte sequence.
- Pattern matching on a bit array with signed integer segments should correctly interpret the two's complement value.
- Pattern matching on bit arrays with little-endian integer or float segments should read bytes in the correct order.
- Negative integer values in sized bit array segments should produce the correct two's complement byte representation.
- The 32-bit float bit array feature should work on all targets, not just as a target-specific feature.

## Why This Matters

Binary protocols, file formats, and network data often use specific byte orderings and integer sign conventions. Without correct endianness and signedness support on JavaScript, Gleam programs that manipulate binary data targeting JavaScript will silently produce wrong results or fail to correctly decode incoming byte sequences. Developers need these options to work the same way on JavaScript as on other targets.
