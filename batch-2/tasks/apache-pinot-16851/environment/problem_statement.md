## Description

Pinot's audit logging system currently only captures information about incoming API requests — it does not record what happens after a request is processed. This means there is no way to audit HTTP response codes, request processing durations, or correlate a response back to its originating request.

## Expected Behavior

- When response auditing is enabled via configuration, both the incoming request and its corresponding response should be logged as separate audit events.
- The request and response audit events must share a common unique identifier (a UUID), allowing users to correlate the two events and reconstruct the full lifecycle of any API call.
- The response audit event should capture: the HTTP status code returned, the time taken to process the request (in milliseconds), the API endpoint path, and the HTTP method used.
- The overall audit enable flag should act as a master switch — response auditing should only occur when both the global audit system is enabled and the response-capture flag is explicitly turned on.
- If no request context is available during the response phase (e.g., the request filter was never called), or if the stored context is invalid, the response filter should handle it gracefully without throwing an exception or altering the response.
- Errors encountered during response audit processing must not affect the HTTP response sent to the client.

## Why This Matters

Without response auditing, operators cannot determine from audit logs whether API calls succeeded or failed, how long they took, or trace individual request/response pairs. This makes it difficult to diagnose issues, enforce compliance requirements, or monitor API performance from audit data alone.
