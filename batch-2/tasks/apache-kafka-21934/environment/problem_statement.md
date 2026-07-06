## Description

In Kafka Streams, the session window store supplier currently uses a boolean flag to control whether the store should support headers. This means the same class handles two fundamentally different behaviors, making it harder to distinguish between plain and headers-capable stores at the type level. Additionally, the session store materializer (the DSL-internal component that builds the layered state store hierarchy) always produces a headers-capable store, even when the underlying store is a plain session store.

This creates a mismatch: the materializer should select the appropriate store-builder path based on whether the provided store supplier actually supports headers. Without this, DSL operations that materialize session windows may produce incorrect store hierarchies — wrapping stores in the wrong layer types.

## Expected Behavior

- A dedicated supplier class should exist for time-ordered session stores with headers support, separate from the plain session store supplier.
- The existing plain session store supplier should no longer carry a headers flag; headers support should be expressed through a separate type.
- The session store materializer should detect at build time whether the supplied store supports headers and select the corresponding builder path, producing either a headers-capable store hierarchy or a plain session store hierarchy as appropriate.
- The store layering (metering, caching, change-logging) must be correct in both cases, matching the expected layer ordering for each combination of caching and logging settings.
- When the emit strategy is configured to fire on window close, caching must be automatically disabled regardless of the materialization settings.

## Why This Matters

These changes allow downstream consumers, such as processor-API nodes, to correctly access session window state stores created through the DSL. Without the correct store type detection, the store hierarchy is mismatched, which can prevent processors from accessing the store or cause runtime errors during stream processing.
