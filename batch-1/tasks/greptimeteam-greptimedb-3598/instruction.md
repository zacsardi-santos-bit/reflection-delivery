Implement parallel scan support for append-mode regions in the mito2 storage engine. Ensure the engine can perform unordered scans when parallelism is configured and preserve region configurations upon reopening. Update the scanner to accurately report memtable counts and allow direct access to scan objects for sequential scans.

*   Update the `reopen_region` function:
    *   Accept a fifth parameter for region options (`HashMap<String, String>`).
    *   Forward these options to `RegionOpenRequest` instead of using an empty map.
    *   Ensure all existing call sites pass either the original region options or an explicit empty map.

*   Modify `MitoEngine`:
    *   Add a `scan_region` method with the signature `fn scan_region(&self, region_id: RegionId, request: ScanRequest) -> Result<ScanRegion>`.
    *   Return the intermediate `ScanRegion` object to allow callers to choose the scan strategy explicitly.

*   Enhance `ScanRegion`:
    *   Implement a `seq_scan` method with the signature `pub(crate) fn seq_scan(self) -> Result<SeqScan>`.
    *   Allow callers to explicitly request a sequential ordered scan by consuming `self`.

*   Extend `SeqScan`:
    *   Provide an async `build_stream` method with the signature `pub async fn build_stream(&self) -> Result<SendableRecordBatchStream>`.
    *   Ensure it produces a stream of record batches containing all expected rows for the region.

*   Update `Scanner`:
    *   Implement a `num_memtables` method with the signature `pub(crate) fn num_memtables(&self) -> usize`.
    *   Ensure it correctly counts the number of memtables to be scanned for both sequential and new scan variants.

*   Configure `MitoConfig`:
    *   Use the `scan_parallelism` field to control the number of parallel scan threads.
    *   When `scan_parallelism` is greater than 1 and a region uses append mode, ensure the engine returns all expected rows without enforcing order.
    *   Use a sequential scan path when `scan_parallelism` is set to 1.

*   Ensure region reopening:
    *   After closing and reopening a region with `reopen_region` using the original options, verify that queries return the same expected rows as before the reopen.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.