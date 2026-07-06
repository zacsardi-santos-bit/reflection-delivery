## Description

The deposit tree snapshot loading system currently accepts only a single optional file path as its source. This design can't express more nuanced scenarios — for example, "try fetching a snapshot from a checkpoint sync node first, but fall back to the bundled network snapshot if that fails." There is also no way to distinguish between snapshot sources that are required (missing = configuration error) and sources that are optional (missing = silently skip).

Additionally, the deposit snapshot configuration is scattered across several individual settings that are not grouped together. There is no clear separation between a custom user-specified path, the built-in network-bundled snapshot, and any checkpoint sync-derived snapshot URL.

## Expected Behavior

- The snapshot loader should support multiple ordered sources. It should try each source in turn and use the first one that succeeds.
- Each source should be configurable as either required (throws an error with the path in the message if not found) or optional (silently skipped if not found).
- When no sources are configured, loading should return an empty result without error.
- The deposit snapshot configuration settings should be consolidated into a dedicated configuration object exposing the custom path, the bundled network path, an enabled/disabled flag, and a checkpoint sync URL — each accessible independently.
- When a checkpoint sync URL is provided, the system should derive a deposit snapshot URL from it and treat it as an optional source to try before falling back to the bundled snapshot.
- Providing a custom path should take priority over all other sources, with the bundled and checkpoint sync sources ignored.
- Disabling the deposit snapshot feature should make the bundled path unavailable even if a network was configured.

## Why This Matters

This refactoring makes it possible to seamlessly support checkpoint sync scenarios where a snapshot may be available remotely but needs a reliable local fallback. It also eliminates the previous constraint that forced users to choose between a custom path and the bundled snapshot rather than being able to combine multiple sources gracefully.
