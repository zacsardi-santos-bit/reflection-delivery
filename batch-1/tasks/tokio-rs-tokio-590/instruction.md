Implement a line-splitting codec that supports an optional maximum line length to prevent memory exhaustion attacks. Ensure the codec gracefully handles lines exceeding this limit by returning an error and discarding the over-long line, while continuing to decode subsequent lines normally.

*   Implement the `with_max_length(limit: usize) -> LinesCodec` constructor in `tokio-codec/src/lines_codec.rs`.
    *   Set the `max_length` field as `Some(limit - 1)` to trigger boundary checks before the (limit+1)-th byte.
*   Modify the `decode` method to:
    *   Return an `Err(io::Error)` if a line exceeds the `max_length` within a single scan window.
    *   Enter a discarding mode after an error, skipping bytes until the next newline is found, then resume normal decoding.
    *   Return `Ok(Some(line))` if a line is exactly at the max-length boundary followed by a newline.
    *   Decode lines correctly within the limit, stripping trailing `\r` before `\n`, and return empty lines as an empty string.
    *   Return `Ok(None)` for incremental data that does not exceed the limit within a single decode call's window.
    *   Return `Ok(Some(line))` for a full over-length line when the final chunk arrives with a newline.
*   Implement the `decode_eof` method to:
    *   Return any remaining buffered data as a line if no newline is present.
    *   Return `Ok(None)` if the buffer is empty.
*   Ensure a line one character longer than the limit causes `decode` to return an error without panicking or accessing out-of-bounds memory.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.