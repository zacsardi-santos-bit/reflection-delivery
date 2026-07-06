## Description

The Java interface for cuDF's GPU memory manager currently provides no way to limit how large the memory pool can grow after initialization. When running in pool allocation mode, the pool will expand to consume as much GPU memory as needed, which can be problematic in environments where GPU memory must be shared or reserved for other workloads.

## Expected Behavior

- Users should be able to specify an optional upper bound on the memory pool's maximum size at initialization time.
- If a maximum pool size is specified but is smaller than the initial pool size, initialization should fail immediately with a clear error.
- If a maximum pool size is specified but the chosen allocation mode does not support pools, initialization should also fail immediately with a clear error.
- When no maximum pool size is specified (or a non-positive value is given), the existing behavior should be preserved — the pool grows without an artificial limit.

## Why This Matters

Without this limit, applications sharing a GPU with other processes have no way to prevent cuDF's memory pool from consuming all available GPU memory. The new parameter gives developers a simple, explicit way to cap memory usage at startup, with validation that catches invalid configurations immediately rather than silently ignoring them.
