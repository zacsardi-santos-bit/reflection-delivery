Implement a deduplication middleware for the Watermill messaging library. Create a `Deduplicator` type that can be used both as a handler middleware and a publisher decorator to drop duplicate messages within a configurable time window. Use a pluggable hashing strategy to determine message similarity and a pluggable repository to track seen keys.

Requirements:

*   Define a `Deduplicator` struct with:
    *   `KeyFactory` of type `MessageHasher`
    *   `Repository` of type `ExpiringKeyRepository`
    *   `Timeout` of type `time.Duration`
*   Implement `Deduplicator.Middleware(h message.HandlerFunc) message.HandlerFunc`:
    *   Intercept messages and skip the inner handler for duplicates.
    *   Call the inner handler only for unique messages.
*   Implement `Deduplicator.PublisherDecorator() message.PublisherDecorator`:
    *   Suppress publishing of duplicate messages.
    *   Forward only unique messages to the underlying publisher.
*   Define `MessageHasher` as a function type:
    *   Signature: `func(*message.Message) (string, error)`
*   Implement `NewMessageHasherAdler32(readLimit int64) MessageHasher`:
    *   Compute an Adler-32 checksum of the message payload.
    *   Enforce a minimum read limit of `MessageHasherReadLimitMinimum` (64 bytes).
*   Implement `NewMessageHasherSHA256(readLimit int64) MessageHasher`:
    *   Compute a SHA-256 checksum of the message payload.
    *   Enforce a minimum read limit of `MessageHasherReadLimitMinimum` (64 bytes).
*   Implement `NewMessageHasherFromMetadataField(field string) MessageHasher`:
    *   Read the hash from a named metadata field.
    *   Return an error if the field is absent.
*   Define `ExpiringKeyRepository` interface:
    *   Method: `IsDuplicate(ctx context.Context, key string) (bool, error)`
*   Implement `NewMapExpiringKeyRepository(window time.Duration) (ExpiringKeyRepository, error)`:
    *   Use an in-memory map with `sync.Mutex`.
    *   Implement `Len() int` to return the count of tracked keys.
    *   Automatically purge expired keys after the window duration.
*   Ensure all exported symbols reside in `github.com/ThreeDotsLabs/watermill/message/router/middleware`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.