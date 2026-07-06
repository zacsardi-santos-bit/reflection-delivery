Fix the checksum verification tools to align with standard GNU coreutils behavior by addressing several issues related to error handling, separator interpretation, and mode-specific outputs.

*   Redirect file-not-found errors to stderr:
    *   Ensure that when a file listed in a checksum file does not exist, the error message '<tool>: <filename>: No such file or directory' is printed to stderr.
    *   Print '<filename>: FAILED open or read' to stdout and '<tool>: WARNING: 1 listed file could not be read' to stderr.

*   Handle asterisk (binary mode) and separators correctly:
    *   Accept checksum files using a single-space separator between hash and filename.
    *   Strip a leading asterisk in single-space separator format to indicate binary mode.
    *   Treat a leading asterisk as part of the filename in double-space separator format.

*   Manage directory and algorithm type errors:
    *   Fail with a non-zero exit code and print an error to stderr when a checksum file references a directory.
    *   Print '<checksum-file>: no properly formatted checksum lines found' to stderr when verifying a checksum file with a mismatched algorithm type.

*   Implement status and quiet mode behaviors:
    *   Suppress all output on a successful check when using the status flag (--status --check).
    *   Suppress success output but show failures when using the quiet flag (--quiet --check).

*   Handle incomplete checksum entries:
    *   Print 'no properly formatted checksum lines found' to stderr for incomplete or truncated entries.

*   Ensure strict mode compliance:
    *   Fail and print valid verification results to stdout and a warning about improperly formatted lines to stderr when --strict mode is used with --check.

*   Implement BLAKE2b checksum computation:
    *   Ensure b2sum computes and displays BLAKE2b checksums correctly in tagged format.
    *   For a file containing 'a\n', output 'BLAKE2b (a) = bedfbb90d858c2d67b7ee8f7523be3d3b54004ef9e4f02f2ad79a1d05bfdfe49b81e3c92ebf99b504102b6bf003fa342587f5b3124c205f55204e8c4b4ce7d7c\n' with default 512-bit length and --tag.
    *   Output 'BLAKE2b-128 (a) = b93e0fc7bb21633c08bba07c5e71dc00\n' with --tag -l 128.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.