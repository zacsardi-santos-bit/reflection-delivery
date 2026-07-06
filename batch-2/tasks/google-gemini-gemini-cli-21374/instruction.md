Implement a caching mechanism to optimize repeated authentication checks in a CLI tool by storing and reusing results within a 30-second window. Develop a general-purpose caching utility to support this feature and other potential use cases across the codebase.

*   Implement the `createCache` function in `packages/core/src/utils/cache.ts`:
    *   Return a `CacheService` instance.
    *   Default to Map-backed storage when called with no arguments.
    *   Support both string-keyed and object-keyed storage with options `{ storage: 'map' }` and `{ storage: 'weakmap' }`.

*   Ensure `CacheService` class in `packages/core/src/utils/cache.ts` provides:
    *   `get(key: K): V | undefined` to retrieve values or undefined if absent/expired.
    *   `set(key: K, value: V, ttl?: number): void` to store values, with optional TTL overriding default.
    *   `delete(key: K): void` to remove entries.
    *   `clear(): void` to remove all entries, throwing an error if storage is WeakMap.
    *   `getOrCreate(key: K, creator: () => V, ttl?: number): V` to retrieve or create and store values.
    *   Automatic eviction of rejected Promises when `deleteOnPromiseFailure` is true (default).

*   Implement `setupUser` function caching:
    *   Cache results per (auth-client instance, project-ID) pair.
    *   Bypass cache if project ID changes or after 30 seconds.
    *   Do not cache failed attempts; retry on subsequent calls.

*   Export `resetUserDataCacheForTesting` from `packages/core/src/code_assist/setup.ts`:
    *   Clear the user-data cache for test isolation.

*   Handle specific behaviors:
    *   `CacheService.clear()` must throw 'clear() is not supported on WeakMap storage' for WeakMap-backed caches.
    *   Ensure `createCache` defaults to Map-backed storage, allowing `clear()` without error.
    *   Maintain cache integrity by ensuring rejected Promises are only evicted if still present at rejection time.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.