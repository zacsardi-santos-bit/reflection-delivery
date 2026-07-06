Implement a mechanism to prevent deadlocks and resource contention in the gossip synchronization subsystem when multiple peers request gossip filter synchronization simultaneously. Introduce an atomic flag to track backlog sending and a bounded, non-blocking queue for processing incoming requests asynchronously.

*   Implement the `QueueTimestampRange` method in `discovery/syncer.go`:
    *   Accept a pointer to a `GossipTimestampRange` message.
    *   Return `true` if the message is successfully enqueued, `false` if the queue is full or timestamp queries are disabled.
    *   Ensure the method is non-blocking; drop messages if the queue is full and return `false` immediately.

*   Update the `GossipSyncer` struct in `discovery/syncer.go`:
    *   Add an `isSendingBacklog` field of type `atomic.Bool`.
        *   Set to `true` before starting a backlog-sending goroutine.
        *   Clear to `false` when the goroutine finishes.
    *   Add a `timestampRangeQueue` field of type `chan *lnwire.GossipTimestampRange`.
        *   Set its capacity using `timestampQueueSize` from `gossipSyncerCfg` or default to `defaultTimestampQueueSize`.

*   Modify the `gossipSyncerCfg` struct in `discovery/syncer.go`:
    *   Include a `timestampQueueSize` field of type `int`.
    *   Use this field to determine the capacity of the `timestampRangeQueue`.

*   Export a constant `defaultTimestampQueueSize` in `discovery/syncer.go`:
    *   Set its value to `1` to define the default queue capacity.

*   Ensure `ApplyGossipFilter` in `discovery/syncer.go`:
    *   Returns early with `nil` if `isSendingBacklog` is `true` without acquiring the semaphore or starting another backlog send.

*   Implement a background goroutine in `GossipSyncer.Start()`:
    *   Read from `timestampRangeQueue` and call `ApplyGossipFilter` for each message.
    *   Ensure the goroutine exits cleanly when the syncer stops.

*   Ensure `QueueTimestampRange` can queue up to `timestampQueueSize` messages when the syncer is not running, returning `false` for additional messages beyond capacity.

*   Guarantee thread safety for concurrent access to `QueueTimestampRange` by multiple goroutines, preventing data races.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.