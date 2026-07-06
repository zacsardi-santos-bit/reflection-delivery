I'm running into an issue where writing a Parquet file with a string column containing a very large value produces an unexpectedly huge file.

*   When writing a Parquet file that contains a string or binary column with a very large value (e.g., 1,000,000 characters), the resulting file size must remain small (under 5,000 bytes). This applies to Polars string columns, binary columns, and columns backed by Arrow large_utf8 and large_binary types.

*   By default, Parquet column statistics (minimum and maximum bounds) for string and binary columns must be truncated to at most 64 bytes. For UTF-8 string columns, truncation must respect Unicode character boundaries so the truncated value remains valid UTF-8. For binary columns, truncation is at the raw byte level without regard for UTF-8 encoding.

*   For UTF-8 string minimum statistics: the truncated value must be the longest valid UTF-8 prefix of the original value that fits within the byte limit. For example, 100 ASCII 'A' characters truncated to 64 bytes yields exactly 64 'A' characters; 63 'A' characters followed by a 2-byte Unicode character truncated to 64 bytes yields only the 63 'A' characters (the 2-byte character is excluded to avoid splitting it).

*   For UTF-8 string maximum statistics: the truncated value must be the longest valid UTF-8 prefix fitting within the byte limit, with the last character incremented to form a valid upper bound. Incrementing must preserve the UTF-8 encoded byte length of the character. For example, 100 'A' characters yields max of 63 'A' characters + 'B'; 32 2-byte characters (e.g., U+00E9) at the 64-byte boundary yields max of 31 such characters + the next character (e.g., U+00EA).

*   For binary (non-UTF-8) minimum statistics: the value must be truncated to exactly the byte limit. For example, 100 bytes of 0x41 truncated to 64 bytes yields exactly 64 bytes of 0x41.

*   For binary maximum statistics: the value must be truncated to at most the byte limit, then an upper bound must be produced by incrementing the last non-0xFF byte. If all bytes within the limit are 0xFF, the algorithm must search beyond the limit to find the first non-0xFF byte, increment it, and use that as the upper bound. For example, a 15-byte value consisting of eight 0xFF bytes, one 0x00 byte, and six 0xFF bytes truncated to 1 byte yields max of eight 0xFF bytes followed by 0x01.

*   The truncation length must be configurable via an environment variable named POLARS_PARQUET_BINARY_STATISTICS_TRUNCATE_LEN. The default value when the variable is not set must be 64 bytes.

*   When POLARS_PARQUET_BINARY_STATISTICS_TRUNCATE_LEN is set to 0, statistics truncation must be completely disabled: statistics must reflect the exact minimum and maximum values in the column, regardless of their length.

*   When POLARS_PARQUET_BINARY_STATISTICS_TRUNCATE_LEN is set to a positive integer N, statistics for string and binary columns must be truncated to at most N bytes using the same UTF-8-aware (for strings) or byte-level (for binary) truncation algorithm.

*   Short values that already fit within the truncation length must not be modified: if the original value's byte length is less than or equal to the truncation limit, the statistics value must equal the original value unchanged.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.