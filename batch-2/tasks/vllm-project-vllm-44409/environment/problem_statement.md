## Description

When using hybrid models that split attention across multiple KV cache groups (for example, a combination of full-attention and sliding-window attention layers), a memory-safety bug can cause the same physical KV cache block to be handed out twice during request allocation. This happens when a request hits the local prefix cache in one group while also needing externally-fetched computed blocks in another group. Because external block allocation happens before locally-cached blocks in other groups have been registered and protected, the external allocator can evict those cached blocks and re-issue them — producing duplicate block IDs and corrupted reference counts across groups.

## Expected Behavior

- When a request is allocated across multiple KV cache groups with a mix of local prefix-cache hits and external computed tokens, every physical block ID assigned to the request must be unique across all groups.
- Every block referenced by the request must have a reference count of at least 1 (no block that has been evicted should appear in a request's allocation).
- These guarantees must hold for both 2-group and 3-group hybrid configurations, as well as after a preempt-then-reallocate cycle where the same request ID is freed and then scheduled again.
- After freeing a request, querying its blocks must return empty results for all groups.
- The internal method responsible for registering locally-cached blocks must be separated from the method that allocates new blocks for externally-computed tokens, with local registration always completing for all groups before any external allocation begins.

## Why This Matters

This bug can silently corrupt KV cache state in production deployments running hybrid attention models, causing incorrect inference outputs or crashes. The fix is needed for any workload that combines prefix caching with external KV connectors across multiple attention types.
