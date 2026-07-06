## Description

Kafka Streams window state stores lack support for a headers-aware storage format. Currently, when building stream processing topologies that use time-ordered window stores, there is no way to configure the store to propagate record headers through the full store chain. This is a gap that blocks use cases where header information must be preserved alongside windowed aggregations.

Additionally, the existing materializer components for building window state stores (for both standard tumbling/hopping windows and sliding windows) do not support the headers-aware store format, and need to be updated to correctly select and layer headers-aware wrapper types when this format is configured.

## Expected Behavior

- A headers-aware variant of the time-ordered window store should be available and creatable via the store supplier factory by passing a flag indicating headers support is required.
- The window store materializers (for standard and sliding windows) should correctly build and layer caching, change-logging, and metering wrappers based on the configured materialization options, with full support for the headers-aware format.
- When the store format is configured as "headers", the materializer should use headers-aware store wrapper types throughout the chain (metering, change-logging).
- When the emit strategy is set to emit on window close (rather than on each update), the standard window materializer should use a time-ordered caching store instead of the regular caching store.
- The sliding window materializer should always use a regular caching store regardless of the emit strategy.

## Why This Matters

This change enables Kafka Streams applications to use headers-aware window stores for time-ordered windowed aggregation, supporting use cases that depend on header propagation. The correct layering of store wrapper types is important for both functionality and testability.
