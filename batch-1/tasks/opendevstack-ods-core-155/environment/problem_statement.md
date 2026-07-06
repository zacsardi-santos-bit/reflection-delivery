## Description

The webhook proxy service does not properly handle several error conditions when receiving and processing build requests. When a request body cannot be parsed as valid JSON, the service silently ignores the parsing error and proceeds with incomplete data, leading to confusing downstream failures. When a request payload passes JSON parsing but contains semantically invalid values (such as an empty branch name), no validation is performed and the request is processed blindly. When the downstream system rejects a pipeline creation request, the error is swallowed and the caller receives no meaningful feedback about what went wrong.

## Expected Behavior

- If the request body is not valid JSON, the service should immediately respond with HTTP 400 and a clear error message indicating the JSON could not be parsed.
- If the request payload is valid JSON but contains semantically invalid values (e.g., empty required fields), the service should respond with HTTP 400 and an error message indicating the input is invalid.
- If the downstream system rejects the pipeline creation (for example, returning a 422 status), the service should propagate that status code back to the caller along with a clear error message indicating the pipeline could not be created.
- Requests missing a valid trigger secret (absent, empty, or incorrect) should continue to be rejected with HTTP 401, and no event processing should take place in these cases.

## Why This Matters

Without these error handling improvements, clients of the webhook proxy receive misleading or no feedback when their requests fail at various stages. Operators cannot distinguish between a bad request, a validation failure, and a downstream rejection. Adding proper error reporting at each stage makes the service behave predictably and aids debugging.
