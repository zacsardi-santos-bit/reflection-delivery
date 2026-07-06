Implement support for reading and writing MessagePack data in nushell. Create commands to convert between MessagePack binary data and nushell values, ensuring data integrity through serialization and deserialization. Handle both standard and compressed MessagePack formats, and provide clear error messages for malformed inputs.

*   Implement the `from msgpack` command to:
    *   Parse binary MessagePack input into nushell values, mapping MessagePack types to corresponding nushell types.
    *   Support timestamp extension types, decoding them into nushell datetime values.
    *   Use the `--objects` flag to collect multiple top-level MessagePack values into a nushell list.
    *   Produce error messages for malformed input, including:
        *   'exceeded depth limit' for deeply nested data.
        *   'utf-8' for invalid UTF-8 strings.
        *   'fill whole buffer' for empty or truncated data.
        *   'after end of' for extra data after the top-level value.
        *   'Reserved' for reserved marker bytes.
        *   'integer too big' for uint64 values too large for nushell integers.
        *   'string key' for maps with non-string keys.
        *   'Unknown MessagePack extension' for unsupported extension types.

*   Implement the `to msgpack` command to:
    *   Serialize nushell values to binary MessagePack format.
    *   Ensure data survives a full roundtrip through serialization and deserialization.

*   Implement the `from msgpackz` and `to msgpackz` commands to handle compressed MessagePack data, ensuring roundtrip fidelity.

*   Register a file type opener for `.msgpack` files to automatically decode contents using `from msgpack`.

*   Update the `NuOpts` struct in `crates/nu-test-support/src/macros.rs` to include a `collapse_output: Option<bool>` field, allowing control over output line collapsing in tests.

*   Ensure the `generate.nu` script in `tests/fixtures/formats/msgpack/` can produce fixture files for various test scenarios.

*   Verify that opening a `.msgpack` file with `open` automatically decodes it using `from msgpack`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.