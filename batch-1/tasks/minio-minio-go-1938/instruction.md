Update the retry logic in the minio-go client to exclude Cloudflare-specific HTTP error code 520 from being considered retryable. Ensure that the function `isHTTPStatusRetryable` correctly identifies this code as non-retryable.

*   Modify the `isHTTPStatusRetryable` function in `retry.go`.
    *   Ensure the function signature is `isHTTPStatusRetryable(statusCode int) bool`.
    *   Return `false` when the `statusCode` is 520.
    *   Do not include status code 520 in the list of retryable HTTP status codes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.