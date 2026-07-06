Fix the CSRF reactive extension in your Quarkus application to correctly validate CSRF tokens from the custom request header for POST requests with non-form bodies. Ensure that requests with valid tokens in the header are processed normally, while those with invalid or missing tokens are rejected.

*   Implement CSRF token generation:
    *   Ensure an HTTP GET request to the CSRF token generation endpoint returns HTTP 200.
    *   Set a cookie named 'csrf-token' with the generated token in the response.

*   Validate CSRF tokens for POST requests with form-urlencoded bodies:
    *   Return HTTP 400 if the request lacks a CSRF token form field.
    *   Return HTTP 400 if the CSRF token form field does not match the 'csrf-token' cookie.
    *   Return HTTP 200 and process the request if the CSRF token form field matches the 'csrf-token' cookie.

*   Validate CSRF tokens for POST requests with no body:
    *   Return HTTP 400 if the request lacks the 'X-CSRF-TOKEN' header.
    *   Return HTTP 400 if the 'X-CSRF-TOKEN' header does not match the 'csrf-token' cookie.
    *   Return HTTP 200 and process the request if the 'X-CSRF-TOKEN' header matches the 'csrf-token' cookie.

*   Validate CSRF tokens for POST requests with non-form bodies (e.g., text/plain):
    *   Return HTTP 400 if the request lacks the 'X-CSRF-TOKEN' header.
    *   Return HTTP 400 if the 'X-CSRF-TOKEN' header does not match the 'csrf-token' cookie.
    *   Return HTTP 200 and process the request body normally if the 'X-CSRF-TOKEN' header matches the 'csrf-token' cookie, ensuring the body content is passed to the endpoint handler.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.