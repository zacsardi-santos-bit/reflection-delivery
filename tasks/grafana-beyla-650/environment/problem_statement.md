## Description

The network flow deduplication system currently supports two modes: one that drops duplicate flow observations entirely, and one that passes duplicates through with a flag marking them as duplicates. The "mark as duplicate" mode adds complexity without clear benefit — consumers must handle the duplicate flag themselves, and it can lead to unexpected metric inflation. This mode should be removed, leaving only the drop-duplicate behavior.

Additionally, when a flow is forwarded after deduplication, it still carries interface-specific and direction-specific identifiers from whatever interface first observed it. Since these fields are no longer meaningful after deduplication (the flow could have been seen on any of several interfaces), keeping them set unnecessarily increases metric cardinality and can produce confusing or inconsistent label values. After deduplication, these fields should be cleared to well-defined sentinel values indicating they are not set.

## Expected Behavior

- Deduplication always drops duplicate flows; there is no option to mark them and pass them through.
- Flows forwarded by the deduplicator have their interface and direction fields replaced with special "unset" sentinel values.
- The data-link layer address fields (used for device-level identification) are no longer tracked as part of flow identity.

## Why This Matters

Removing the "mark" mode simplifies the deduplication contract: downstream consumers never need to check whether a flow is a duplicate. Clearing interface and direction after deduplication prevents these per-interface attributes from randomly varying across flow reports, reducing metric cardinality and making exported metrics more predictable.
