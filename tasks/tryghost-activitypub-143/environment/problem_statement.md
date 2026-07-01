# Add Google Cloud Pub/Sub Message Queue Integration

## Description

We need to integrate a cloud-based message queue into the ActivityPub service so that federation activities can be published and consumed asynchronously. Currently, there is no mechanism to enqueue work items for distributed processing or to subscribe to incoming messages from a cloud messaging service. This gap makes the service less resilient and harder to scale.

## Expected Behavior

- A message queue class should be able to publish messages to a cloud messaging topic, including metadata about the message. Messages with a delay set should be silently skipped rather than published immediately.
- The message queue should support listening for incoming messages delivered via an internal event system. When a message is received and processed successfully, it should be acknowledged. If processing fails, the message should be negatively acknowledged so it can be retried.
- An initialization helper should create and configure the message queue, verifying that the required topic and subscription actually exist before returning. If the topic does not exist, the helper should fail with a clear error indicating which topic is missing. If the subscription does not exist, it should fail similarly.
- The initialization helper should only pass configuration options to the underlying cloud client when they are explicitly provided — absent options should not be forwarded at all.

## Why This Matters

Without this integration, the service must handle all incoming federation activities synchronously during HTTP requests, which can cause timeouts and data loss under load. With a message queue in place, activities can be reliably queued, processed asynchronously, and automatically retried on failure. Verifying topic and subscription existence at startup ensures misconfigured deployments fail fast with actionable error messages rather than silently misbehaving at runtime.
