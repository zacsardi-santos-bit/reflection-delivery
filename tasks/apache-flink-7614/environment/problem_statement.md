## Description

The Flink Kafka consumer does not reliably close its internal partition discoverer component when the consumer shuts down due to a failure or cancellation. This leads to resource leaks — underlying connections and threads associated with partition discovery are left open even after the consumer has stopped.

## Expected Behavior

- When partition discovery itself fails (throws an exception), the partition discoverer should still be closed when the consumer is shut down.
- When the Kafka fetcher cannot be created (throws an exception during initialization), the partition discoverer should still be closed on consumer shutdown.
- When the Kafka fetcher fails during operation, the partition discoverer should be woken up first (to interrupt any blocking operations) and then closed.
- During normal consumer cancellation — when the consumer is explicitly stopped while running — the partition discoverer should be properly closed.

In all shutdown scenarios, the partition discoverer must reach a fully closed state by the time the consumer's close operation completes.

## Why This Matters

Failing to close the partition discoverer means that resources such as connections and background threads are leaked whenever the consumer encounters a failure or is cancelled. This can cause instability in long-running Flink jobs that restart consumers. Properly managing the partition discoverer lifecycle ensures clean shutdown in both normal and error scenarios.
