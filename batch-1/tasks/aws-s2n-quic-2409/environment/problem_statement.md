# Add a Worker Manager for TCP Connection Handling

## Description

The TCP stream server needs a component to coordinate a fixed pool of connection handlers. Currently there is no mechanism to limit how many connections are processed concurrently, no way to reclaim handler slots from connections that have been running for an excessive amount of time, and no efficient mechanism to drive handlers without polling all of them on every iteration.

## Expected Behavior

- A manager should maintain a bounded pool of connection handler slots with a fixed capacity set at construction time.
- The manager should track how many slots are currently in use and expose that count.
- Inserting a new connection should succeed when a free slot exists, and fail when all slots are occupied and no slot has been active long enough to be preempted.
- When all slots are occupied but at least one has been active longer than a configurable maximum duration, inserting a new connection should preempt the longest-running slot rather than reject the new connection.
- Preempting a slot should replace the existing connection without changing the total count of active slots.
- Each handler should be driven asynchronously — the manager should only revisit a handler when that handler signals it has pending work, not on every poll cycle.
- When a handler finishes processing its connection, its slot should become free and the active count should decrease accordingly.

## Why This Matters

Without this manager, the TCP server has no principled way to bound resource usage, handle long-running connections fairly, or avoid busy-polling idle handlers. With it in place, the server can safely accept new connections within a fixed resource envelope and make progress under load without unnecessary polling.
