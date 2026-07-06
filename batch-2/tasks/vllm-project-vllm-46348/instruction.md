Implement input validation for the chat completions and completions API endpoints to ensure that requests with an empty list of allowed token IDs are rejected with a clear error message. Update the server to return a 400 Bad Request response when such invalid requests are detected.

*   Validate requests to the `/v1/chat/completions` endpoint:
    *   If the `allowed_token_ids` field is an empty array, respond with HTTP 400 Bad Request.
*   Validate requests to the `/v1/completions` endpoint:
    *   If the `allowed_token_ids` field is an empty array, respond with HTTP 400 Bad Request.
*   Ensure the error response body for both endpoints:
    *   Is valid JSON.
    *   Contains an `error` object with:
        *   A `type` field set to `"invalid_request_error"`.
        *   A `message` field that includes the substring `"allowed_token_ids should not be empty"`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.