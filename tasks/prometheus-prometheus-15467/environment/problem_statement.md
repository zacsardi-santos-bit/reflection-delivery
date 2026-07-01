## Description

Prometheus supports a special kind of histogram where users define their own bucket boundaries rather than relying on the standard exponential schema. However, these user-defined bucket boundaries are not currently preserved when histogram data is written to the write-ahead log (WAL). When the WAL is replayed — after a restart, during checkpointing, or when tailing for remote write — the boundary definitions are silently lost, making it impossible to correctly interpret the histogram data.

## Expected Behavior

- Histograms that use custom bucket boundaries should be written to the WAL using a dedicated record type that includes the bucket boundary values.
- When reading these records back (replay after crash, checkpoint, or remote write tailing), the custom bucket boundary values should be fully restored — not silently dropped.
- The WAL reader should recognize both the standard histogram record type and the new custom-bucket record type, decoding both correctly using the same decoding path.
- All WAL consumers (head reload, agent WAL replay, checkpoint creation, out-of-order buffer replay, WAL watcher) should handle the new record type without special-casing at each call site.

## Why This Matters

Without this fix, any histogram with custom bucket boundaries that is appended to Prometheus loses its bucket definitions across restarts and is not correctly forwarded via remote write. This makes the data unusable for the purpose of query or aggregation since the bucket boundaries are essential metadata for interpreting the counts.

Supporting custom-bucket histograms in the WAL is a prerequisite for reliable storage and remote write of this histogram variant.
