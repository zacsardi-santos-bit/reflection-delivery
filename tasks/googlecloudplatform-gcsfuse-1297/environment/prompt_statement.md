I'm working on the gcsfuse project and we want to move our GCS object stat caching implementation out of an external dependency and into our own internal package. Right now the stat cache and the caching bucket wrapper both live in a third-party library, which makes it hard to add features or fix issues ourselves.

I need to create a new internal package that provides two things: a stat cache that maps object names to GCS object records with time-based expiration and LRU eviction, and a bucket wrapper that uses this cache to avoid hitting GCS on every stat request.

The cache should handle both positive entries (object exists) and negative entries (object was not found). Insertions into the cache should follow a "best wins" rule where older object versions do not overwrite newer ones. Entries expire after a configurable TTL, and the least recently used entry is evicted when the cache is at capacity.

The bucket wrapper should invalidate cache entries before any mutating operation (create, copy, compose, update, delete), then insert the result into the cache on success. For stat requests, it should serve from cache when there's a hit, fall back to GCS on a miss, and support an option to bypass the cache entirely and fetch directly from GCS regardless of what's cached. When GCS reports an object is not found, a negative entry should be added to the cache so that subsequent requests for the same name are also answered from cache without hitting GCS again.

The new package should live within an internal subdirectory of the project dedicated to cloud storage caching, and any existing code that imported this functionality from the external library should be updated to use the new internal package instead.
