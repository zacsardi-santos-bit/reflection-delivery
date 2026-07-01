I'm refactoring the storage layer of the reduce pipeline's data-forwarding component, and I need help with a few interconnected changes.

Right now, the persistent store manager lives inside the buffer queue manager rather than being passed directly to the data-forwarding component. I want to separate these two: the store manager should be created independently and passed explicitly to the data forwarder as its own parameter, so both the buffer queue manager and the data forwarder can share the same store manager instance without awkward coupling.

The interface for replaying persisted messages also needs to change. Currently replay requires you to specify a batch count and returns a slice plus an end-of-file flag — which is cumbersome because callers have to manage looping. I'd like replay to use a channel-based streaming API instead: the store sends messages through a channel and closes it when done, and any errors come through a separate error channel. An empty store's replay should close its channels immediately.

Similarly, when discovering what stores already exist (for crash recovery at startup), the current API returns only partition identifiers, forcing callers to do extra lookups. I want discovery to return actual, ready-to-use store instances directly so they can be replayed immediately.

Finally, writing to the buffer queue needs a boolean flag to indicate whether the write should also be persisted to backing storage. This is necessary to distinguish between a regular write (persist = true) and a replay write (persist = false, since we don't want to re-persist messages during replay).

The filesystem-backed write-ahead log implementation also needs to be reorganized: its package should move from the old location to a new one, and the constructor renamed accordingly. The sync duration and batch size fields that were previously nested inside a sub-struct should become direct fields on the WAL object itself. There should also be a dedicated constructor for opening a WAL in write-only mode given a file path and configuration parameters.
