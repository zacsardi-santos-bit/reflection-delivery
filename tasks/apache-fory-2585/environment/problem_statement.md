## Description

The Fory Rust serialization library is missing several low-level encoding primitives and has a correctness issue in its compatible-mode deserializer, all of which are required for proper cross-language interoperability with Java and Python.

**Missing string utilities:** There is currently no way to determine whether a string is representable in the Latin-1 single-byte encoding, nor to compute how many bytes it would occupy in that encoding. These utilities are needed to select the most compact string representation at serialization time.

**Missing 36-bit integer codec:** The buffer layer provides variable-length codecs for 32-bit and 64-bit integers, but there is no analogous codec for 36-bit unsigned integers. A compact 1-to-5 byte encoding for values up to the 36-bit maximum is needed.

**Compatible-mode default value handling:** When deserializing in compatible mode (schema evolution), the deserializer must initialize fields that are absent from the serialized data with their default values. Currently this does not use the standard default-value mechanism, so types must derive the standard default trait for missing-field initialization to work correctly, and some collection/optional field types (such as optional-string vectors) are not handled properly.

## Expected Behavior

- A function that returns whether a string is entirely Latin-1 encodable, and a companion function that returns the Latin-1 byte length (or a sentinel value of -1 for non-Latin-1 strings).
- Encode and decode methods for 36-bit variable-length unsigned integers that round-trip exactly across the full value range.
- Compatible-mode deserialization correctly initializes missing fields to their default values when the type implements the standard default trait.
- Optional-string collections can be used as field types in compatible-mode structs.

## Why This Matters

These primitives are building blocks for the cross-language serialization wire format. Without them, Rust cannot correctly read or write binary data produced by the Java serializer, making cross-language round-trip tests impossible to pass.
