## Description

The NIXL-backed cache storage layer has a file descriptor leak. Every time a cache read or write operation is performed, the underlying implementation opens file handles to access storage files but never closes them afterward. Over time, these handles accumulate and can exhaust the system's file descriptor limit, causing subsequent operations to fail.

## Expected Behavior

- File handles opened during cache read and write operations must be properly closed after each operation completes, regardless of whether the operation succeeded or failed.
- The cache storage configuration object should capture all relevant parallelism dimensions (tensor parallelism, pipeline parallelism, attention checkpoint parallelism) as well as optional plugin configuration.
- Plugin selection and configuration should be expressed through the configuration object rather than as a separate constructor argument.
- Registering files with the cache backend should work by supplying file paths directly, without requiring a caller to perform any intermediate conversion.

## Why This Matters

Without proper cleanup, long-running services that perform many cache operations will accumulate open file handles until the OS limit is hit, at which point all further I/O fails. This makes the NIXL storage backend unusable for production workloads.
