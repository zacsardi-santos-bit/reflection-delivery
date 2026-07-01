## Description

The scanner gRPC service currently accepts requests with any number of keys — including zero — without validating the count. This means callers can send requests with an empty key list or with an unbounded number of keys, which leads to unnecessary downstream processing (or worse, abuse through oversized batches). All key-management operations share this problem.

## Expected Behavior

- Every scanner operation should immediately reject a request that contains no keys, returning a clear invalid-argument error.
- Every scanner operation should immediately reject a request that contains more keys than a defined per-request maximum, returning a clear invalid-argument error.
- Valid requests (non-empty, within the limit) should continue to work exactly as they do today.

## Why This Matters

Without these checks, the service has no protection against malformed or abusive requests. A client bug that accidentally sends an empty key list silently wastes resources instead of getting a useful error. A client sending a huge batch could put unexpected load on the system. Adding explicit validation makes the API more predictable for callers and safer for the service.

## Additional Context

The scanner gRPC service exposes operations to retrieve scan results, register viewing keys, clear stored results, and delete keys. All four operations need consistent input validation with a shared, documented limit on how many keys may appear in a single request.
