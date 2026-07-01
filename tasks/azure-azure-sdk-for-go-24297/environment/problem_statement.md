## Description

The Azure Service Bus Go SDK currently has no distributed tracing support for messaging operations. When developers use the SDK to send, receive, or settle messages, those operations produce no spans and carry no trace context, making it impossible to observe Service Bus activity in distributed tracing systems alongside the rest of an application's telemetry.

## Expected Behavior

- A new internal tracing package should be added to the Service Bus SDK that handles distributed tracing concerns.
- The package should be able to propagate trace context through message properties, so that trace information injected by a sender can be extracted by a receiver.
- The package should extract relevant message attributes (such as message identifier, conversation identifier, delivery count, and enqueue timestamp) and attach them as span attributes.
- Operations should be classified into the correct span category according to standard messaging observability conventions: message creation and single-message sends should use a "producer" span kind; batch sends, receives, settles, and session operations should use a "client" span kind; unclassified operations should use an "internal" span kind.
- When no operation name is provided or no tracer is configured, starting a span should be a no-op that returns the original context unchanged.
- The span name should be composed of the operation name and the destination (queue or topic name) when available.

## Why This Matters

Without this tracing layer, Service Bus operations are a black box to observability tools. Developers cannot see how long operations take, correlate messages across services, or investigate failures through distributed trace views. This package forms the foundation for exposing all Service Bus SDK operations to distributed tracing frameworks.
