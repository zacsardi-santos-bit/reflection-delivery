## Description

The Kafka mesh proxy filter only forwards message keys and values when routing produce requests to upstream Kafka brokers. Any headers included in the original messages are silently dropped and never reach the target cluster's consumers.

## Expected Behavior

- When a client sends a Kafka message that includes headers through the mesh proxy, those headers must be preserved and forwarded to the upstream Kafka cluster.
- Consumers reading messages from the upstream cluster should receive the same key, value, and headers as originally sent by the producer.
- If the internal header conversion step fails for any reason, the send operation should be aborted and the caller notified of the failure immediately — rather than proceeding with an incomplete message or leaking resources.

## Why This Matters

Many Kafka applications use message headers for cross-cutting concerns such as distributed tracing, routing metadata, or custom application attributes. Silently dropping these headers when traffic passes through the proxy breaks any application that relies on this metadata being present at the consumer side. Properly propagating headers end-to-end is required for the proxy to be transparent to the applications using it.
