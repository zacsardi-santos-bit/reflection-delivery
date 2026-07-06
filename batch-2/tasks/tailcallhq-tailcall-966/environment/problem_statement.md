## Description

The current server context construction always creates its own internal cache instance, which makes it impossible to swap in a different cache backend for different deployment targets. For example, a cloud-worker deployment might need to use a cloud-hosted key-value store for caching, while the native server should continue using an in-memory TTL cache. Right now there is no way to inject an alternative implementation — the cache is hard-wired inside the constructor.

## Expected Behavior

- The server context constructor should accept the cache as an explicit parameter rather than creating it internally.
- A new helper function should be provided so native environments can easily create the appropriate in-memory cache and pass it in.
- The cache interface should be defined as a shared trait so that different environments (native, cloud, etc.) can each provide their own implementation.
- All existing integration tests and end-to-end tests must continue to pass after this change.

## Why This Matters

Making the cache injectable allows the same server context logic to run across deployment targets without duplication. Cloud-hosted deployments can supply a platform-specific cache backend, while native servers use the provided in-memory implementation — all without changing the core server logic.
