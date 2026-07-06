Implement a reusable buffer pool in the `internal/buf` package to manage temporary memory allocations efficiently. Ensure the pool supports pre-allocated segments, safe allocation and release of buffers, and proper resource cleanup.

*   Implement the `Pool` struct in `internal/buf/pool.go` to manage buffer allocations.
    *   Create a new pool using `NewPool(ctx context.Context, segmentSize int, poolID string) *Pool`.
        *   `segmentSize` specifies the maximum size of each segment.
        *   `poolID` is a string identifier for the pool.
    *   Implement `Pool.AddSegments(n int)` to add `n` new segments of size `segmentSize` to the pool.
    *   Implement `Pool.Allocate(n int) Buf` to allocate a buffer of exactly `n` bytes.
        *   Ensure `Buf.Data` has `len == n` and `cap == n`.
        *   Return a pooled `Buf` (`IsPooled() == true`) when memory is available from pool segments.
        *   If called on a nil `*Pool`, return a heap-allocated `Buf` with `IsPooled() == false`.
    *   Implement `Pool.Close()` to release resources and stop any background goroutines.
        *   Ensure it is safe to call with `defer`.

*   Implement the `Buf` struct in `internal/buf/pool.go` to represent allocated memory.
    *   Include a `Data` field of type `[]byte`.
        *   Ensure `Data` has `len == cap ==` the size requested in `Allocate`.
    *   Implement `Buf.IsPooled() bool` to indicate if the buffer was allocated from a pool segment.
    *   Implement `Buf.Release()` to return the buffer to the pool.
        *   Ensure it is safe to call on non-pooled `Buf` (`IsPooled() == false`).

*   Ensure the pool is memory-efficient under concurrent load.
    *   With 30 goroutines each performing 1,000,000 allocations and releases of 100,000 bytes from a 20-segment pool of 1,000,000 bytes each, total new heap allocation should be less than 1,000,000 bytes.

*   Update the block manager's `Close` method to properly close all associated resources, including buffer pools.
    *   Ensure `Close` returns a nil error when called in a stress test scenario.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.