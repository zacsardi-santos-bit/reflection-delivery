## Description

The mito2 storage engine currently uses a single sequential scan strategy for all region types, including append-mode regions. For append-mode tables, this is unnecessary — rows don't need to be deduplicated or returned in strict timestamp order, so data sources can be scanned in parallel for much better throughput.

Additionally, when a region is reopened (e.g. after engine restart or during testing), the utility always opens it with empty options, discarding the original region configuration. This means regions that were created with custom options — such as append mode or compaction settings — lose those settings on reopen, leading to incorrect behavior.

## Expected Behavior

- The engine should automatically use an unordered parallel scan strategy for append-mode regions, scanning memtables and SST files concurrently when parallelism is configured to be greater than 1.
- Callers should be able to directly access and trigger a sequential scan from the intermediate region scan object, without going through the full scanner dispatch path.
- The region reopen utility should accept the original region options and pass them through to the open request, so the region's configuration is preserved across close/reopen cycles.
- The scanner should correctly report the number of memtables it is scanning regardless of which scan strategy is in use.

## Why This Matters

Append-mode tables are a common use case for time-series workloads where write throughput and scan speed matter more than strict ordering. Without parallel scan support, these tables leave significant performance on the table. The region reopen bug also causes subtle correctness issues in tests and production scenarios where regions with custom options are restarted.
