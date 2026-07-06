## Description

The RabbitMQ source connector blocks indefinitely while waiting for the next message to arrive. Because there is no timeout on this wait, the source cannot respond to cancellation signals, which means stopping a Flink job that reads from RabbitMQ — for example, triggering a savepoint and stopping — may hang forever.

## Expected Behavior

- Users should be able to configure a delivery timeout on the RabbitMQ connection so that the source periodically unblocks and checks for cancellation.
- The timeout should be configurable in milliseconds, or with an explicit time unit for convenience.
- If no timeout is configured, a sensible default (30 seconds) should be used.
- Providing a negative timeout value should be rejected with a clear error.
- When a job is stopped or cancelled, the RabbitMQ source should exit cleanly without throwing an exception.

## Why This Matters

Without a delivery timeout, Flink jobs using the RabbitMQ source cannot be stopped gracefully. This is particularly problematic in production environments where savepoint-based upgrades and controlled shutdowns are common. Adding a configurable timeout gives users control over how responsive the source is to job lifecycle events, while providing a safe default for users who do not need to customize it.
