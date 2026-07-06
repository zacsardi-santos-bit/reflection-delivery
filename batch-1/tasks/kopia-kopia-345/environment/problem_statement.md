## Description

Kopia performs many short-lived memory allocations during data operations such as encryption and compression — allocating new buffers on every operation puts significant pressure on the garbage collector, especially under high-throughput, concurrent workloads.

We need a reusable buffer pool that allows pre-allocating a fixed set of memory segments that can be efficiently shared and recycled across goroutines, eliminating per-operation heap allocations for these temporary buffers.

## Expected Behavior

- A buffer pool can be created with a configurable segment size and an identifier string.
- Segments can be added to the pool to grow its capacity.
- Allocating from the pool returns a buffer with the exact requested size (both length and capacity), and the buffer reports itself as pooled.
- Releasing a buffer returns it to the pool for immediate reuse.
- When allocating from a nil or unavailable pool, the system falls back gracefully to a heap allocation, and the returned buffer correctly indicates it is not pooled.
- Releasing a non-pooled buffer is safe and does not panic.
- The pool can be closed cleanly, stopping any background work it performs.
- The block manager properly closes all associated resources (including any buffer pools) when its own close method is called, and this close operation must succeed without errors.

## Why This Matters

Without reusable buffer pools, each data operation allocates temporary memory from the heap, generating garbage collection overhead that compounds badly under concurrent load. With a pool in place, the memory footprint for temporary buffers becomes essentially constant after warmup, enabling much higher throughput with lower latency. Additionally, ensuring the block manager closes its resources properly prevents goroutine and memory leaks in long-running or repeatedly-opened sessions.
