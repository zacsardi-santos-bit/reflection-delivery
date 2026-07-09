## Description

The session caching system currently has no concept of read-only access. When a session runs in app/presentation mode, caching is simply disabled — there's no way for these sessions to benefit from work that was already cached during development. At the same time, app sessions should never be allowed to overwrite the cache, since they are consumer-facing and should not interfere with the developer's saved computation state.

## Expected Behavior

- Introduce a read-only caching mode that allows sessions to load pre-computed results from the cache without writing anything back.
- Editing sessions should continue to use full read-write cache access as before.
- App/run mode sessions should use read-only cache access: they can load from the cache but cannot modify it (no writing, no path renaming).
- A new runtime configuration option should allow users to opt in to having app mode sessions served from cached data. When disabled, app sessions skip the cache entirely; when enabled, they read from it.

## Why This Matters

Developers working interactively can accumulate expensive computation results in the cache. Without this change, those results are never visible to end users viewing the app — the app always recomputes from scratch. With read-only caching for app sessions and a configuration toggle, teams can share cached results with users while keeping cache writes strictly controlled to editing sessions.
