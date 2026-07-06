I'm working on the Azure Service Bus Go SDK and I need to add distributed tracing support. Right now there's no way for the SDK to participate in distributed traces — when messages are sent or received, nothing is reported to a tracing backend. I'd like to create a new internal tracing package that handles this.

The package needs to do a few things: propagate trace context through message application properties so that trace headers injected on the sender side can be extracted on the receiver side; extract meaningful message metadata (like the message identifier, conversation identifier, delivery count, and enqueue time) and expose them as trace span attributes; and correctly classify each operation into the right span kind based on standard messaging observability conventions.

For span classification, creating a message or sending a single message should be a "producer" span, while batch sends, receives, settlement operations, and session-related operations should be "client" spans. Operations that don't fit a known category should fall back to "internal" spans.

Starting a span should be a no-op (returning the original context unchanged) when no tracer is configured or no operation name is given. When a destination (queue or topic name) is available, the span name should include both the operation name and the destination.

The carrier that wraps a message for trace propagation should handle nil messages gracefully without panicking.
