Implement the ability for the `bytes starts-with` command to handle streaming binary input incrementally, comparing chunks against a specified pattern as they arrive. Additionally, create a test utility to output raw binary data for testing purposes.

*   Update the `BytesStartsWith` command's `run` method in `crates/nu-command/src/bytes/starts_with.rs` to:
    *   Accept streaming binary data from `PipelineData::ExternalStream` and process chunks incrementally.
    *   Return 'true' if the stream starts with the specified binary pattern, 'false' if it doesn't match or if the stream ends before the pattern is fully matched.
    *   Produce an error with the message 'Input type not supported' for plain string inputs, with no stdout output.
    *   Handle both `Value::String` and `Value::Binary` chunks, treating string chunks as their UTF-8 byte representation.
    *   Maintain an offset counter to track progress through the pattern across multiple chunks.

*   Ensure the command handles:
    *   Short streams and long streams without error.
    *   Mixed content types in streams, including binary and string chunks.

*   Implement the `repeat_bytes` function in `src/test_bins.rs` to:
    *   Accept alternating hex-encoded byte strings and integer counts as arguments.
    *   Write each decoded byte sequence to stdout, repeated according to the specified count.
    *   Handle arbitrary byte values, including null bytes.
    *   Register `repeat_bytes` in `src/main.rs` under the `"repeat_bytes"` match arm.

*   Ensure the `bytes collect` and `bytes build` commands work correctly for constructing patterns in streaming tests.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.