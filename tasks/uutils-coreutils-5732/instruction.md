Update the `head` utility to handle negative count arguments on non-seekable files and improve error messaging for large count values. Ensure compatibility with Linux virtual filesystem files like those under `/proc`.

*   Modify the error handling:
    *   When a byte or line count argument is too large to convert, output the error message: "head: out of range integral type conversion attempted: number of -bytes or -lines is too large" to stderr, followed by a newline.
    *   This applies to both the bytes flag (-c) and the lines flag (--lines / -n).

*   Enhance functionality for non-seekable files:
    *   Implement logic to handle negative byte counts on non-seekable files, such as those in `/proc`.
    *   Ensure the command exits successfully and outputs all content except the last N bytes when invoked with a negative byte count on these files.
    *   Use a streaming/sequential read approach for non-seekable files while maintaining the existing seek-based approach for regular files.

*   Make all necessary changes in the file `src/uu/head/src/head.rs` to support both seekable and non-seekable files, applying the correct reading strategy based on file characteristics.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.