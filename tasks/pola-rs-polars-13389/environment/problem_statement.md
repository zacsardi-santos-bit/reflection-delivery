## Description

The SQL interface does not support inline binary data literals, making it impossible to express raw byte values directly in SQL queries. Users working with binary-typed columns cannot write bit-pattern or hexadecimal notation directly in SELECT statements or WHERE clause filters. Several standard SQL string measurement functions and their common synonyms are also missing, including a way to measure string length in bits rather than characters or bytes. Additionally, registering an empty or placeholder table in the SQL context is not supported, which limits the ability to write structural queries without real data.

## Expected Behavior

- SQL queries should support bit string notation (sequences of zeros and ones) that get converted to the corresponding bytes, following standard binary number interpretation (MSB-first, zero-padded to whole bytes)
- SQL queries should support hexadecimal notation (pairs of hex digits) that get converted to binary bytes, with case-insensitive digit recognition
- These inline binary values should work in both SELECT expressions and WHERE clause comparison filters against binary-typed columns
- Invalid binary literals should produce clear error messages: non-binary characters in a bit string should be rejected, and an odd number of hex digits should be rejected
- A common plain-English word for the binary data type should be recognized as a valid type alias when casting columns
- Standard alternative names for the character-length function should be recognized and return character counts
- A function to return the bit-length of a string (byte count × 8) should be available
- Creating an SQL context with a placeholder (empty) table should be supported

## Why This Matters

Without inline binary literal support, users must resort to workarounds to work with fixed binary values in SQL queries. The missing string function synonyms and bit-length measurement make the SQL interface feel incomplete compared to standard SQL dialects, adding friction for users migrating from other databases.
