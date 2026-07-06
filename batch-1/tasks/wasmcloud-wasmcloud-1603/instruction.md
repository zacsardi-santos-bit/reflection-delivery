Implement a new Rust crate that provides derive macros for automatic serialization and deserialization of custom structs and enums for the wRPC transport layer. Use the derive macros to enable automatic encoding and decoding of types, eliminating the need for manual implementation.

*   Create the `wrpc-transport-derive` crate located at `crates/wrpc-transport-derive/`.
    *   Re-export the `EncodeSync` and `Receive` derive macros from `wrpc-transport-derive-macro`.
    *   Re-export `wrpc_transport::{Encode, EncodeSync, Receive}`.
    *   Expose a `deps` module containing `anyhow`, `async_trait`, `bytes`, `futures`, and `wrpc_transport`.

*   Implement the `EncodeSync` derive macro:
    *   Signature: `#[derive(EncodeSync)]` on structs or enums.
    *   For structs:
        *   Serialize fields in declaration order using `wrpc_transport::EncodeSync` trait methods.
        *   Use `encode_sync_option` for `Option<T>` fields and `encode_sync_list` for `Vec<T>` fields.
        *   Directly call `encode_sync` for all other field types.
    *   For enums:
        *   Encode the variant's zero-based index as a discriminant using `wrpc_transport::encode_discriminant`.
        *   Encode any variant fields in declaration order after the discriminant.
        *   Support unit, unnamed/tuple, and named struct variants.

*   Implement the `Receive` derive macro:
    *   Signature: `#[derive(Receive)]` on structs or enums.
    *   For structs:
        *   Decode fields in declaration order using `Receive::receive_sync`.
        *   Ensure compatibility with fields of types `u8`, `u32`, `String`, `Option<String>`, and `Vec<String>`.
    *   For enums:
        *   Receive the discriminant using `wrpc_transport::receive_discriminant`.
        *   Reconstruct the correct variant based on the discriminant value.
        *   Support all enum variant styles: unit, unnamed/tuple, and named struct variants.

*   Ensure roundtrip encoding and decoding:
    *   Encoding a value with `encode_sync` and then decoding it with `Receive::receive_sync` must return a value equal to the original.
    *   After decoding, ensure the leftover byte buffer has `remaining() == 0`, indicating all bytes are consumed exactly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.