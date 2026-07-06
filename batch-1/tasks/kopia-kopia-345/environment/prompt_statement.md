I'm working on a backup tool that does a lot of in-memory data processing — things like encryption and compression — and I'm seeing significant garbage collection pressure under high concurrency. The root cause seems to be that we're allocating new temporary byte buffers for every operation instead of reusing them.

I'd like to add a reusable buffer pool to the internal utilities of the project. The pool should let me pre-allocate a fixed number of fixed-size memory segments upfront, then hand out slices from those segments on demand and reclaim them when callers are done. The key requirements are:

- Allocating from the pool should return a buffer with exactly the requested size (both length and capacity), and callers should be able to tell whether a buffer came from the pool or was heap-allocated as a fallback.
- Releasing a buffer should be safe to call whether or not the buffer actually came from the pool.
- If the pool is nil or unavailable, allocation should fall back gracefully to a plain heap allocation without panicking.
- The pool should be closeable to stop any background work it spawns.
- Under realistic concurrent load — many goroutines each doing millions of allocate/release cycles — using the pool should result in dramatically less total heap allocation compared to allocating fresh each time (on the order of kilobytes, not megabytes).

I also need the block manager to properly close its resources when its own close method is called. Right now, closing the block manager doesn't clean up the buffer pools it holds internally, which could lead to goroutine leaks. The close method should return a nil error when everything shuts down cleanly.
