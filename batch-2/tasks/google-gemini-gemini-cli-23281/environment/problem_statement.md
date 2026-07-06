## Description

The telemetry tracing system requires a manual callback to close spans for streaming operations. Callers that produce streaming responses must hold onto a provided "end span" function and call it themselves once the stream is fully consumed — but this is easy to forget or misuse, and it couples streaming logic to span lifecycle management. We should instead make span management fully automatic, even for streaming results.

Additionally, there is no safe, consistent way to attach arbitrary values (strings, objects, numbers, booleans) as telemetry attributes. Long strings or large objects can bloat telemetry payloads, and multi-byte unicode characters (like emoji) can cause incorrect truncation if not handled at grapheme-cluster boundaries. We need a utility that truncates values safely before attaching them.

Finally, there is currently no way to control whether sensitive prompt content (inputs and outputs) is included in telemetry spans. Some deployments may want to opt out of logging prompt data for privacy or compliance reasons.

## Expected Behavior

- When a traced operation returns a stream (async iterable), the span should automatically close when the stream finishes — whether it completes normally or throws an error.
- Callers should not need to receive or call an "end span" function; it should be handled transparently.
- A utility function should safely prepare values for telemetry: passing through numbers and booleans as-is, serializing objects to a text representation, truncating any string or serialized representation that exceeds a maximum length, and appending a suffix that includes the original length. Multi-byte unicode characters should be handled correctly.
- A configuration option should control whether prompt content is included in telemetry spans.

## Why This Matters

Incorrect span lifecycle management can lead to unclosed spans and telemetry leaks. Automatic lifecycle management removes an entire class of bugs and simplifies callers. Safe, length-bounded telemetry values also prevent accidental data bloat and potential privacy issues with large prompt payloads in trace exports.
