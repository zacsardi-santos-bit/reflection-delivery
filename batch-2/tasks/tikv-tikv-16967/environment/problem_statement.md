## Description

When TiKV's async I/O feature is enabled (by setting a non-zero I/O thread pool size), the raft engine's batch compression threshold should be automatically tuned to a more appropriate value for optimal performance. Currently, if a user enables async I/O without explicitly configuring the compression threshold, the system continues using the library default, which is not tuned for concurrent async I/O workloads. This leads to suboptimal write performance that users would only discover after careful manual profiling and tuning.

## Expected Behavior

- When async I/O is enabled and the user has not explicitly configured the raft engine's batch compression threshold, the configuration validation step should automatically set the threshold to 4KB, which has been shown to perform better under async I/O conditions.
- When the user has explicitly configured the batch compression threshold to any value, that value should be preserved during validation — the auto-tuning should only apply to unconfigured (default) settings.
- When async I/O is disabled, the batch compression threshold should remain at its default value even if it has not been explicitly configured.

## Why This Matters

Users who enable async I/O expect good out-of-the-box performance without needing to know about the interaction between async I/O and raft engine compression settings. The validation layer is the right place to apply this automatic tuning, so the system self-configures correctly based on the user's I/O configuration choices.
