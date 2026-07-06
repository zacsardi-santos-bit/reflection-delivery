## Description

When requesting a thread's details and asking for the conversation turns to be included in the response, the endpoint fails for any thread that doesn't yet have a file-based history path (rollout path). This affects actively running threads that store their history in an in-memory store, or any thread that hasn't yet materialized its on-disk representation.

The error is misleading: threads that are fully functional and have persisted items in the thread store are rejected as if they were unsupported "ephemeral" threads, simply because no rollout file path has been resolved yet.

## Expected Behavior

- The thread-read endpoint should support returning conversation turns for any non-ephemeral loaded thread, regardless of whether a file-based rollout path has been assigned.
- When a thread's history is stored in an in-memory or alternative backing store, those items should be readable via the thread-read endpoint with the include-turns option.
- A client should be able to retrieve a complete snapshot of thread metadata and conversation history in a single request for any active thread.

## Why This Matters

This is particularly relevant for threads that are loaded into memory and actively running — clients should be able to get the full history without needing to use a separate pagination endpoint, and without the request being rejected solely due to the absence of a rollout file path.
