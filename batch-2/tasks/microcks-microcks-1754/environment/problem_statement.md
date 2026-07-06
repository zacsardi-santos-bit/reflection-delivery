## Description

Microcks collects distributed trace data from services as they handle requests, but currently there is no way for clients to receive real-time notifications when new trace data arrives. Clients must poll to check for new traces, and there is no built-in way to filter traces by service name, operation, or originating client address.

## Expected Behavior

- When a root span is stored (i.e., the start of a new trace), a notification event should be published internally so that other components can react to new trace arrivals.
- A utility should be provided for matching service names, operation names, and client addresses against patterns, supporting exact matches, a universal wildcard, and regular expression patterns.
- When a pattern is not a valid regular expression, matching should fall back to exact string equality.
- A subscription manager should allow clients to register for a filtered live stream of trace events, specifying service name, operation name, and client address patterns. Each subscriber receives only the traces that match their registered filters.
- When a subscriber registers, they should immediately receive a heartbeat confirmation that the subscription is active.
- If a trace arrives but has no associated spans, no events should be forwarded to subscribers.
- If a network error occurs while delivering an event to a subscriber, the subscription should be gracefully closed.
- Periodic heartbeats should be sent to all active subscribers to keep their connections alive.

## Why This Matters

Without real-time notifications and filtering, developers cannot efficiently monitor specific services or operations as they execute. This change enables live observability of distributed traces within Microcks without requiring repeated polling, and gives subscribers fine-grained control over which traces they receive.
