Implement a solution to handle JSON documents with very large string values in Dolt's JSON storage system. Ensure that such documents are stored and retrieved correctly without truncation or errors, even when the string exceeds the internal chunk size limits.

*   Update the `SerializeJsonToAddr` function in `go/store/prolly/tree/json_chunker.go`:
    *   Detect when a JSON document contains a string value exceeding the maximum allowed chunk size.
    *   Implement a fallback mechanism to store the document as a plain blob instead of using the indexed prolly tree format.
    *   Ensure the function does not return an error for oversized strings.

*   Ensure retrieval and querying of large-string JSON documents:
    *   Use `NewJSONDoc` to construct a `JSONDoc` from a stored root hash, including documents stored as blobs.
    *   Implement the `ToIndexedJSONDocument` method to convert a `JSONDoc` to an `IndexedJsonDocument`:
        *   Ensure it succeeds without error for blob-stored documents.
        *   Ensure the resulting `IndexedJsonDocument` is queryable.

*   Implement correct querying of large JSON string values:
    *   Use `LookupJSONValue` from the `types` package in `go/go-mysql-server` to look up values in an `IndexedJsonDocument`.
    *   Ensure the function returns the full, untruncated string value for large strings stored as blobs.

*   Preserve the full original string content:
    *   Ensure that converting a looked-up value to a string produces exactly the same characters as the original, with no truncation.
    *   Support string lengths of at least 2,097,152 characters.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.