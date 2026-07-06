Implement enhanced error handling and text extraction for application update downloads. Ensure that invalid compressed data is clearly reported and extract readable text from binary responses.

*   Implement a new caching reader in `pkg/util/caching-reader.go`:
    *   Create a `NewCachingReader(r io.Reader, cacheSize int) *CachingReader` function to construct a `CachingReader`.
    *   Ensure `CachingReader` implements the `io.Reader` interface:
        *   The `Read(buf []byte) (int, error)` method must delegate to the underlying reader and update a rolling cache of the last `cacheSize` bytes.
    *   Implement `GetCachedBytes() []byte` in `CachingReader`:
        *   Return the most recently cached bytes, up to `cacheSize`.
        *   Return an empty or nil slice if no bytes have been read.
        *   Maintain the cache correctly across multiple `Read` calls.

*   Implement text extraction in `pkg/util/extract.go`:
    *   Create `ExtractReadableText(input []byte) string` to scan a byte slice for printable ASCII characters, including tab and newline.
    *   Discard text runs shorter than 5 characters.
    *   Join multiple retained text runs with ' ... ' (space-dot-dot-dot-space).
    *   Return an empty string if no qualifying text runs are found.

*   Update error handling in `pkg/upstream/`:
    *   Modify `downloadReplicatedApp` to return an error message containing 'failed to create gzip reader' when the HTTP response body is not valid gzip-compressed data.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.