## Description

Thread objects returned by the server do not currently expose whether they represent a temporary, in-memory-only session or a persistent saved thread. Clients have no reliable way to distinguish between these two thread types from the thread data itself — they would have to infer it indirectly from the presence or absence of a file path, which is fragile.

## Expected Behavior

- Every thread object returned from any server operation (thread creation, thread read, thread list, thread resume) should include an explicit boolean field indicating whether the thread is ephemeral or persistent.
- For threads that are created as ephemeral (not saved to disk), this field should be true.
- For threads that are backed by a saved rollout on disk, this field should be false.
- This flag must be present both in the typed API response and in the serialized JSON wire format.

## Why This Matters

API clients need to be able to tell at a glance whether a given thread will persist beyond the current session. Without this explicit indicator, clients must rely on heuristics (like checking whether a path is set), which is error-prone and couples clients to internal server behavior. Making this distinction explicit improves the reliability and clarity of the API.
