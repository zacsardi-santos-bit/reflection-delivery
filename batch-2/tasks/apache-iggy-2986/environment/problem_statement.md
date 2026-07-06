## Description

The Go client library for this project currently lacks a shared, standardized mechanism for encoding and decoding binary data in the wire protocol format. Without a reusable codec layer, every part of the codebase that needs to serialize or deserialize numeric types and strings must implement its own byte-packing logic, leading to duplication and inconsistency.

## Expected Behavior

- A binary encoder that sequentially appends values into a byte buffer using little-endian byte order, supporting unsigned integer types of various widths, single-precision floats, raw byte slices, and strings both with and without length prefixes.
- A binary decoder that sequentially reads values out of a byte buffer using the same little-endian conventions, mirroring all the encoder's types and string variants.
- Both the encoder and decoder should support serialization of arbitrary objects through the standard binary marshaling interface.
- Both types should accumulate errors silently across multiple operations, so callers can chain a series of reads or writes and inspect the error only once at the end.
- When a buffer overrun or a serialization failure occurs, the resulting error should include the source location (file and line) where the failing operation was called, making it easy to pinpoint problems during development and debugging.
- The encoder should offer a way to pre-allocate its internal buffer to a known size so that sequences of writes with known total size avoid memory reallocations.

## Why This Matters

Having a single, well-tested codec utility eliminates repetitive ad-hoc binary handling throughout the Go client, makes the serialization layer more reliable, and gives developers clear error messages with source locations when something goes wrong.
