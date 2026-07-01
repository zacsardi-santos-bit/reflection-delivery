## Description

Working with the wRPC transport layer requires serializing and deserializing custom Rust types to send them across the wire. Currently, developers must manually implement the encoding and decoding logic for every struct and enum they want to transport — there is no way to derive these implementations automatically. This leads to a lot of repetitive boilerplate and is especially painful for types with optional fields, lists, or enums that have multiple different variant shapes.

## Expected Behavior

A new crate should be added that provides derive macros for the wRPC transport encoding and decoding traits. Developers should be able to annotate their custom structs and enums with derive attributes to get automatic serialization support. Specifically:

- Structs with fields of common types (integers, strings, optional values, and lists) should be supported.
- Enums with unit variants (no data), tuple-style unnamed variants, and named struct-style variants should all be supported, including enums that mix variant styles.
- Encoding a value and then decoding it should faithfully reproduce the original value with no data loss.
- The decoding process should consume exactly the bytes that were encoded — no more, no less.

## Why This Matters

Without derive support, every new type that needs to cross the wRPC transport boundary requires hand-written serialization code. This slows down development, introduces opportunity for bugs (mismatched field order, wrong encoding for optional vs. required fields), and makes the codebase harder to maintain. Derive macros solve all of these problems at once.
