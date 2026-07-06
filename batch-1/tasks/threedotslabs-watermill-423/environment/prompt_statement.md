I'm working with the watermill Go messaging library and I need to add a deduplication middleware to the existing `message/router/middleware` package. In my system, the same logical message can arrive more than once due to at-least-once delivery, and I want to silently drop duplicates within a sliding time window without the handler ever seeing them.

I need a `Deduplicator` type that can be used either as a handler middleware (to intercept messages before they reach the handler) or as a publisher decorator (to suppress duplicates before they are published downstream). The deduplication key should be computed by a pluggable hashing function — I'd like at least two built-in options based on the message payload (a fast checksum and a collision-resistant one), plus a way to pull the hash directly from a metadata field when it has been pre-computed upstream.

The hashing functions should have a configurable read-limit that caps how many bytes of the payload are read, but they should enforce a sensible minimum so that passing zero doesn't silently produce poor results.

I also need a default in-memory store for tracking seen keys with automatic expiry. It should be thread-safe and clean up expired entries on its own. I'd like to be able to inspect how many keys are currently tracked in the store (useful for testing and monitoring).

The implementation should live in the `github.com/ThreeDotsLabs/watermill/message/router/middleware` package alongside the existing middleware components.
