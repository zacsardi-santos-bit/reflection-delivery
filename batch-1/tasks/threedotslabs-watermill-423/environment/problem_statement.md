# Add Deduplication Middleware to Watermill

## Description

Watermill currently provides several useful middleware components (retry, poison queue, throttle, etc.) but lacks built-in support for **message deduplication**. In distributed systems it is common for the same message to be delivered more than once — for example, due to network retries or at-least-once delivery guarantees. Without deduplication, handlers may process the same logical message multiple times, causing duplicate side effects.

We need a new `Deduplicator` middleware that drops duplicate messages within a configurable time window. The similarity between messages should be determined by a pluggable hashing strategy (`MessageHasher`), and the seen-key storage should be provided by a pluggable `ExpiringKeyRepository`.

## Expected Behavior

- A `Deduplicator` struct with `KeyFactory` (MessageHasher), `Repository` (ExpiringKeyRepository), and `Timeout` fields.
- A `Middleware(h HandlerFunc) HandlerFunc` method on `Deduplicator` that intercepts messages: if the message hash has been seen within the deduplication window the message is silently dropped (not passed to the inner handler); otherwise the inner handler is called.
- A `PublisherDecorator() message.PublisherDecorator` method that provides the same deduplication behaviour on the publisher side.
- Two built-in `MessageHasher` constructors based on the message payload:
  - `NewMessageHasherAdler32(readLimit int64)` — fast Adler-32 checksum.
  - `NewMessageHasherSHA256(readLimit int64)` — slower but more collision-resistant SHA-256.
  - Both constructors must enforce a minimum read limit (`MessageHasherReadLimitMinimum = 64` bytes): if a lower value is passed the minimum is used silently.
- A third `MessageHasher` constructor, `NewMessageHasherFromMetadataField(field string)`, that reads the hash from a named metadata field rather than computing one. It must return an error when the field is absent.
- A default in-memory `ExpiringKeyRepository` implementation provided by `NewMapExpiringKeyRepository(window time.Duration)`. The implementation must expose a `Len() int` method reporting how many keys are currently tracked, and must automatically purge expired keys after the window elapses.

## References

- Watermill middleware package docs: https://pkg.go.dev/github.com/ThreeDotsLabs/watermill/message/router/middleware
- Watermill message package (PublisherDecorator, HandlerFunc): https://pkg.go.dev/github.com/ThreeDotsLabs/watermill/message
