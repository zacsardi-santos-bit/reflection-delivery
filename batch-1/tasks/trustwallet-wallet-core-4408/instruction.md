Implement the necessary changes in the Rust crypto library to correct memory ownership issues in the FFI layer. Adjust function signatures and add missing functionalities to ensure safe usage from foreign-language callers and prevent memory leaks.

*   Update the UUID generation function:
    *   Modify the `tw_uuid_random` function in `rust/wallet_core_rs/src/ffi/utils/uuid_ffi.rs` to return a mutable (owned) pointer to a heap-allocated null-terminated string.
    *   Ensure the returned string is a valid UUID in the format of five hyphen-separated groups of 8, 4, 4, 4, and 12 hexadecimal characters.
    *   Guarantee that each call to this function returns a distinct UUID.

*   Add a destroy function for the bit reader utility:
    *   Implement the `tw_bit_reader_delete` function in `rust/wallet_core_rs/src/ffi/utils/bit_reader_ffi.rs`.
    *   Ensure it accepts the opaque pointer returned by `tw_bit_reader_create` and properly frees all associated resources.
    *   Make sure this function can be called after both successful and error-producing operations.

*   Update the hex encoding function:
    *   Ensure the `encode_hex` function in `rust/tw_encoding/src/ffi/` returns a mutable (owned) pointer to a heap-allocated string.
    *   Confirm that encoding without prefix produces lowercase hexadecimal and encoding with prefix prepends '0x'.

*   Update the base64 encoding function:
    *   Ensure the `encode_base64` function in `rust/tw_encoding/src/ffi/` returns a mutable (owned) pointer to a heap-allocated string.
    *   Support both standard and URL-safe encoding variants.

*   Adjust base32 encoding and decoding functions:
    *   Modify the `encode_base32` and `decode_base32` functions in `rust/tw_encoding/src/ffi/` to accept a read-only (const) pointer for the alphabet parameter.
    *   Ensure the alphabet parameter is of type `*const c_char`, reflecting that it is only read, not modified.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.