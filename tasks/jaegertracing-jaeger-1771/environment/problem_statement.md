## Description

The badger storage backend computes timestamps redundantly during span write operations. Each index entry (service, operation, duration, tags) independently recomputes the current time and converts it to the required format. This means a single span write triggers many separate time calls, leading to redundant type conversions and, in theory, slight inconsistencies if the clock ticks between calls.

Additionally, the in-memory cache uses signed integer types for storing expiration timestamps, while the underlying storage engine natively uses unsigned integers for its own expiration mechanism. This type mismatch requires unnecessary casting and can cause subtle comparison issues.

## Expected Behavior

- Timestamps required for a span write (start time and expiration time) should be computed once per write call and reused across all index entries and cache updates.
- The in-memory service and operation caches should store timestamps using the same unsigned integer type that the storage engine uses natively, removing the need for conversion.
- The functions that build index keys and trace keys should accept the pre-computed timestamp value directly rather than performing the conversion themselves.
- The cache update call should receive the pre-computed expiration time rather than recomputing it internally.
- Trace query results should be returned in consistent newest-to-oldest order.

## Why This Matters

Redundant timestamp computation wastes CPU cycles on every write (a hot path) and risks subtle bugs from mismatched integer types between the cache and the storage layer. Centralizing the computation and aligning types makes the write path simpler, faster, and less error-prone.
