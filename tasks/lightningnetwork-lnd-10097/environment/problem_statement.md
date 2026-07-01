## Description

When multiple peers simultaneously request gossip filter synchronization, the node can enter a deadlocked or severely degraded state. Under rate-limited conditions, many goroutines can compete to send the same backlog of messages at the same time, blocking the gossip processor and preventing normal operation.

## Current Behavior

- When several peers apply gossip filters concurrently, multiple goroutines can all attempt to send the same backlog of messages simultaneously.
- This causes redundant work, resource contention, and under rate-limiting can result in deadlocks.
- There is no mechanism to prevent a second goroutine from starting a backlog send while another is already in progress.
- Gossip filter requests arrive synchronously and may block the gossiper while waiting for the rate limiter.

## Expected Behavior

- Only one goroutine should ever be actively sending the gossip backlog at a time. If one is already running, additional requests should be dropped gracefully rather than queueing up.
- An atomic flag should track whether a backlog send is in progress, so concurrent callers can detect this and return immediately.
- Incoming gossip filter requests should be placed into a bounded asynchronous queue and processed one at a time, preventing the gossiper from blocking.
- When the queue is full, new requests should be dropped with a warning rather than causing the caller to block.
- The queue processor must exit cleanly when the node shuts down.

## Why This Matters

Nodes with many simultaneous peer connections are especially vulnerable: a burst of simultaneous gossip filter requests can stall the entire gossip subsystem. The fix ensures that gossip filter handling is non-blocking, resilient to overload, and free of concurrent backlog duplication.
