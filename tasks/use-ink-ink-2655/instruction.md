Implement a non-allocating variant of the Solidity ABI encoding API that writes directly into a pre-allocated byte buffer. Ensure this variant is available at all levels of the encoding hierarchy and supports nested collection types.

*   Update the `SolTypeEncode` trait:
    *   Add the method `fn encode_to(&self, buffer: &mut [u8]) -> usize` in `crates/primitives/src/sol/types.rs`.
    *   Ensure it writes encoded bytes into a pre-allocated buffer and returns the number of bytes written.
    *   The number of bytes written must equal `self.tokenize().total_words() * 32`.
    *   The output must match `SolTypeEncode::encode(&self)`.

*   Update the `SolEncode` trait:
    *   Add the method `fn encode_to<'a>(&'a self, buffer: &mut [u8]) -> usize` in `crates/primitives/src/sol.rs`.
    *   Ensure it writes encoded bytes into a pre-allocated buffer and returns the number of bytes written.
    *   The output must match `SolEncode::encode(&self)`.
    *   Default implementation should delegate to `SolTypeEncode::encode_to`.

*   Update the `SolParamsEncode` trait:
    *   Add the method `fn encode_to<'a>(&'a self, buffer: &mut [u8]) -> usize` in `crates/primitives/src/sol/params.rs`.
    *   Ensure it writes parameter sequence bytes into a pre-allocated buffer and returns the number of bytes written.
    *   For dynamic types, exclude the top-level 32-byte offset, writing `token.tail_words() * 32` bytes.
    *   For non-dynamic types, write `token.head_words() * 32` bytes.
    *   Output must match `SolParamsEncode::encode(&self)`.

*   Implement a public free function:
    *   `pub fn encode_sequence_to<T: for<'a> SolParamsEncode<'a>>(value: &T, buffer: &mut [u8]) -> usize` in `crates/primitives/src/sol.rs`.
    *   Serve as a convenience wrapper for `SolParamsEncode::encode_to`.
    *   Ensure it produces identical output to `SolParamsEncode::encode_to`.

*   Ensure the `Encodable` trait:
    *   Located in `crates/primitives/src/sol/encodable.rs`, is publicly accessible.
    *   Exposes a `DYNAMIC: bool` constant indicating dynamic sizing.

*   Ensure the `SolTokenType` trait:
    *   Located in `crates/primitives/src/sol/types.rs`, is publicly accessible.
    *   Exposes a `TokenType<'enc>` associated type implementing `Encodable`.

*   Support encoding and decoding for nested collection types:
    *   Nested fixed-size arrays, dynamic arrays, and tuples must produce standard-compatible output.
    *   Ensure round-trip correctness through decode.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.