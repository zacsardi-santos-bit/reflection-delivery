## Description

When running vLLM with FlashInfer attention kernels, autotuning is performed at startup to select optimal kernel configurations for the current hardware. However, the results of this autotuning are currently stored in a temporary directory and deleted after each run. This means the autotuning work is repeated on every startup even when the hardware and configuration have not changed, adding unnecessary overhead.

## Expected Behavior

- Autotune results should be persisted to a stable, versioned cache directory that survives across runs.
- The default cache location should be organized by the FlashInfer version, hardware architecture identifier, and a hash of the relevant runtime configuration, all rooted under the main vLLM cache root.
- Users should be able to override the default cache location by setting a dedicated environment variable to a custom directory path. When this override is set, the cache file should be placed directly under that directory (keyed by a configuration hash), bypassing the default layout.
- If the override is not set, the default directory structure should be used.

## Why This Matters

Repeated autotuning on every startup is wasteful. A persistent, content-addressed cache means the expensive tuning step only needs to happen once for a given hardware and configuration combination, reducing startup time on subsequent runs. The override option gives operators control over cache placement in shared or containerized environments.
