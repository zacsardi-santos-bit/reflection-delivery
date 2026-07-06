## Description

Nushell has no built-in support for reading or writing the MessagePack binary serialization format. MessagePack is a widely-used compact binary format that is common in databases, caching systems, messaging queues, and network protocols as a language-neutral alternative to JSON. Without native support, users who work with tools or services that produce MessagePack data have no convenient way to inspect, manipulate, or generate that data from within nushell.

## Expected Behavior

- Users should be able to open a file in MessagePack format and have nushell automatically decode it into native nushell values (null, booleans, integers, floats, strings, binary data, lists, records, and datetimes).
- There should be commands to explicitly convert raw binary MessagePack data into nushell values, and to convert nushell values back into MessagePack binary data.
- Data should survive a roundtrip through serialization and deserialization without loss.
- A streaming mode should allow reading a sequence of multiple top-level MessagePack values from a single source, returning them as a list.
- A compressed variant of MessagePack should also be supported, with corresponding convert-to and convert-from commands, and full roundtrip fidelity.
- Clear error messages should be produced for malformed or unsupported input, including: data that is too deeply nested, strings with invalid encoding, truncated or empty data, extra data after the end of a message, reserved or unknown markers, integers too large to represent, non-string map keys, and unknown or malformed extension types.

## Why This Matters

This enables nushell to integrate seamlessly with systems that use MessagePack as their data exchange format, making it practical to inspect, transform, or generate MessagePack data directly from the shell without external tools.
