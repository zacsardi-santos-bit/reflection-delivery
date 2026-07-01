## Description

There is currently no way to perform multiple authorization checks in a single API request. Each check must be made individually, which creates unnecessary overhead for callers that need to evaluate many authorization decisions at once.

We need a new API endpoint that accepts a list of check items bundled together in one request. Each check item must carry a unique identifier so that the caller can correlate each result back to the check that produced it. The server should return all results in a single response, keyed by those identifiers.

## Expected Behavior

- Clients can submit multiple authorization check items in a single request
- Each check item must include a non-empty, unique identifier; requests with missing or duplicate identifiers should be rejected
- The request must contain at least one check item; an empty list should be rejected
- The request must not exceed a configured maximum number of checks; exceeding this limit should result in a clear error indicating how many were received and what the maximum is
- If an individual check fails, its error should be captured in the per-item result rather than failing the entire batch
- If no specific authorization model is specified, the latest model should be used — and the results should be identical to explicitly specifying that model
- Errors from individual checks (such as invalid relations, invalid tuples, resolution depth exceeded, evaluation failures, throttling, deadline exceeded, or other internal errors) should be mapped to well-defined per-item error codes
- The authorization routing layer must recognize the batch check operation and apply the same permission requirements as a regular check operation

## Why This Matters

Without batching, clients making many authorization decisions in a single user interaction must serialize or fan out requests themselves, adding latency and complexity. A dedicated batch endpoint reduces the number of round trips and simplifies client code for bulk authorization scenarios.
