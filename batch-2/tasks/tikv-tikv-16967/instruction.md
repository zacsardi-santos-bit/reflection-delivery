Implement automatic tuning of the raft engine's batch compression threshold in TiKV's configuration validation logic when async I/O is enabled. Ensure that the threshold is adjusted appropriately based on user configurations and the state of async I/O.

*   Detect if async I/O is enabled by checking if `store-io-pool-size` is greater than 0.
    *   If enabled and `batch-compression-threshold` is at the library's default value, set `batch_compression_threshold` to 4KB during configuration validation.
*   Preserve user-specified `batch-compression-threshold` values:
    *   If the user has explicitly set `batch-compression-threshold` to a non-default value, retain this value regardless of async I/O status.
*   Handle scenarios where async I/O is disabled:
    *   If `store-io-pool-size` is 0 and `batch-compression-threshold` is at the default, do not modify the threshold.
*   Ensure the raft engine configuration includes a `batch_compression_threshold` field that is readable and can be updated in-place during validation when conditions for automatic tuning are met.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.