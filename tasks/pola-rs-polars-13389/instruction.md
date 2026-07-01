Implement support for inline binary data literals in SQL queries, add missing string functions, and enable the creation of placeholder tables. Enhance the SQL interface to handle bit and hexadecimal string literals, recognize type aliases, and support additional string functions.

*   Enable casting of string Series to binary data type:
    *   Convert string values to byte-level representation.
    *   Ensure empty strings become empty bytes, and None values remain None.
    *   Restore original string values when casting back from binary to string.

*   Support bit string literals in SQL using `b'...'` notation:
    *   Interpret bit strings as binary numbers (MSB-first), zero-padded to whole bytes.
    *   Examples: `b''` produces empty bytes, `b'1001'` produces `b'\x09'`, `b'11101011'` produces `b'\xeb'`.

*   Support hexadecimal string literals in SQL using `x'...'` notation:
    *   Interpret each pair of hex digits as one byte (case-insensitive).
    *   Examples: `x''` produces empty bytes, `x'FF'` produces `b'\xff'`, `x'4142'` produces `b'AB'`.

*   Allow bit and hex string literals in SQL WHERE clauses for binary-typed columns:
    *   Treat `b'10'`, `x'02'`, and similar representations as equivalent for byte value `0x02`.

*   Raise errors for invalid literals:
    *   Raise `ComputeError` for non-binary characters in bit strings with message: 'bit string literal should contain only 0s and 1s'.
    *   Raise `ComputeError` for odd number of hex digits with message: 'hex string literal must have an even number of digits'.

*   Recognize 'bytes' as a type alias for binary data type:
    *   Use '::bytes' in cast expressions to produce Binary dtype columns, equivalent to 'blob' or 'VARBINARY'.

*   Support additional SQL string functions:
    *   Recognize `CHAR_LENGTH` and `CHARACTER_LENGTH` as synonyms for `LENGTH`, returning character counts.
    *   Implement `BIT_LENGTH` function to return bit count (byte length × 8).

*   Enable SQLContext constructor to accept `None` for named frame keyword arguments:
    *   Treat `None` as an empty table, allowing queries to reference table names without data rows.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.