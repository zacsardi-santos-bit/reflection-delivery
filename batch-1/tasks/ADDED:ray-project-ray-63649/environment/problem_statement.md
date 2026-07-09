## Description

The singleton thread router's bridge mechanism leaks replica slots when a caller's task is cancelled at a specific point in the request dispatch cycle.

When a request arrives at the router, the router internally selects a replica on a dedicated internal event loop. The slot selection uses an async context manager — the entry phase reserves a slot on that internal loop, and the exit phase releases it. Once the entry phase completes, the bridge is supposed to hand ownership of that context manager back to the caller's outer loop.

The race condition: if the caller's task is cancelled **after** the entry phase has completed (and a slot is reserved on the internal loop) but **before** the bridge has transferred ownership to the outer loop, the slot is never released. The exit phase never runs, and the slot remains held indefinitely — even though no request is actually using it.

## Expected Behavior

- If a caller is cancelled during this transition window, the bridge must detect the abandoned selection and explicitly run the context manager's exit phase to release the slot.
- The release must be triggered by the bridge itself, not left to garbage collection (which may be delayed or may trigger the wrong cleanup path).
- No slots should be leaked after a cancelled request.

## Why This Matters

In long-running serving deployments, repeated cancellations in this window accumulate leaked slots. Over time, this can exhaust the available slot capacity and prevent new requests from being routed to healthy replicas — effectively causing a slow-motion denial of service that is very hard to diagnose.
