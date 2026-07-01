## Description

Several index-related operations in the data coordinator client silently discard transport-level connection errors. When the remote service is unreachable or experiences a network failure during these operations, the error is swallowed and the caller receives no indication that anything went wrong — they may receive what appears to be a successful (or empty) response instead.

This is a correctness issue: callers cannot distinguish between genuine success and a network failure, making it impossible to implement proper error handling or retry logic for index operations such as creating, describing, dropping, or querying index state and build progress.

## Expected Behavior

- When a low-level communication error occurs during any data coordinator client operation (such as index creation, index state query, or index drop), the error should be returned to the caller.
- When the remote service returns a response with an error status embedded in the response body (but without a communication failure), the client should return the response as-is so the caller can inspect the status code, while returning no Go-level error.

## Why This Matters

Silent error swallowing makes failures invisible. Callers that rely on error returns to decide whether to retry, log, or fail fast cannot do so correctly when transport errors are discarded. This can lead to data inconsistency or stalled operations that appear to have succeeded when they have not.
