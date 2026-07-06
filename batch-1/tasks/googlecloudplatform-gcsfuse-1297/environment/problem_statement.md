## Description

The project currently relies on an external library for its object stat caching layer when mounting Google Cloud Storage buckets. This makes it difficult to customize caching behavior, add new features, or fix bugs in the caching logic since the code lives outside the repository.

We should bring the stat caching implementation in-house by creating our own package within the project. This gives us full ownership over the caching behavior, makes it easier to test independently, and allows us to extend it in ways the external package does not support.

## Expected Behavior

- A new internal package provides an object stat cache that stores GCS object records, each with a time-based expiration.
- The cache uses LRU eviction when it reaches its configured capacity.
- Insertions follow a "best wins" rule: an existing cache entry is not replaced by a new one with an older object version.
- Negative cache entries (recording that an object was not found) can be stored and are returned as not-found responses.
- A bucket wrapper uses this cache to serve repeated stat requests without hitting GCS, with cache invalidation on mutations (create, copy, compose, update, delete).
- When a stat request explicitly indicates the result should come directly from GCS, the cache is bypassed entirely.
- After the TTL elapses, cached entries are treated as expired and GCS is contacted again.

## Why This Matters

Owning this code in-house allows the team to add features like forced cache bypass, improve test coverage, and adjust the caching strategy to fit the project's evolving requirements — none of which is possible when depending on an external package.
