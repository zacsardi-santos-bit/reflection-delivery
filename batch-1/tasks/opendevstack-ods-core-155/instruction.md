Implement robust error handling in the webhook proxy service to improve feedback for build requests. Ensure proper responses are returned for JSON parsing errors, semantic validation failures, and downstream system rejections. Update the pipeline creation function interface to support these changes.

*   Update the `CreatePipelineIfRequired` method in the `Client` interface:
    *   Change the signature to return `(int, error)` instead of just `error`.
    *   Ensure the integer represents the HTTP status code from the upstream system.

*   Enhance the `/build` endpoint to handle various error conditions:
    *   Return HTTP 400 with 'Cannot parse JSON\n' if the request body is not valid JSON.
    *   Use the `IsValid()` method on `Event` to check for semantic validity.
        *   Return HTTP 400 with 'Invalid input\n' if the payload is semantically invalid (e.g., empty required fields).
    *   Propagate upstream error status codes:
        *   If `CreatePipelineIfRequired` returns an error, respond with the same status code and 'Could not create pipeline\n'.

*   Implement trigger secret validation:
    *   Return HTTP 401 Unauthorized if the `trigger_secret` query parameter is missing, empty, or incorrect.
    *   Ensure no event processing occurs in these cases.

*   Ensure successful requests:
    *   A valid request with a correct `trigger_secret` and a parseable, semantically valid payload should return HTTP 200.

*   Implement the `IsValid()` method on `Event`:
    *   Return `false` if the event kind is unrecognized, the pipeline name is shorter than 3 characters, or any required fields (Project, Namespace, Repo, Component, Branch) are empty.

*   Modify the `HandleRoot` HTTP handler in `jenkins/webhook-proxy/main.go`:
    *   Check for JSON parsing errors and return HTTP 400 with 'Cannot parse JSON\n' if parsing fails.
    *   Ensure error propagation from `CreatePipelineIfRequired` by using the status code and returning 'Could not create pipeline\n' on failure.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.