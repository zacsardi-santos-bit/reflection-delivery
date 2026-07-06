Implement support for converting BSON documents, arrays, and values to strings with a configurable byte-length limit in the MongoDB Go driver. Ensure that the conversion respects UTF-8 character boundaries and efficiently handles large documents for logging purposes.

*   Implement the `Truncate` function in the `internal/bsoncoreutil` package:
    *   Accepts a string and an integer `width`.
    *   Returns a string truncated to at most `width` bytes.
    *   For `width <= 0` or an empty input string, return an empty string `""`.
    *   If the input string's byte length is less than or equal to `width`, return the full string.
    *   Ensure truncation respects UTF-8 multi-byte character boundaries, excluding incomplete characters.

*   Update the `Document` type in the `x/bsonx/bsoncore` package:
    *   Add a `StringN(n int) string` method.
    *   Return `""` for `n <= 0` or when the document is empty/invalid.
    *   For valid documents with `n > 0`, return the Extended JSON representation truncated to `n` bytes.

*   Update the `Array` type in the `x/bsonx/bsoncore` package:
    *   Add a `StringN(n int) string` method.
    *   Return `""` for `n <= 0`.
    *   Return `"[]"` for an empty array with `n >= 2`.
    *   For non-empty arrays with `n > 0`, return the Extended JSON representation truncated to `n` bytes.

*   Update the `Value` type in the `x/bsonx/bsoncore` package:
    *   Add a `StringN(n int) string` method.
    *   Return `""` for `n <= 0`.
    *   For `n > 0`, return the Extended JSON representation of the value truncated to `n` bytes.
    *   Ensure truncation respects UTF-8 character boundaries for string-typed values.

*   Implement the `FormatDocument` function in the `internal/logger` package:
    *   Accepts a BSON document as `bson.Raw` or `[]byte` and a width parameter.
    *   Returns a string representation of the document limited to the specified width.
    *   Ensure it is callable as `FormatDocument(bs, 1024)` where `bs` is `[]byte` from `bson.Marshal`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.