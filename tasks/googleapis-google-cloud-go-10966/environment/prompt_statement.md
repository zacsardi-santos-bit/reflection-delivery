I'm working on the fast object-listing library for cloud storage and I need to fix and refactor several things.

The parallel listing path (work-stealing algorithm) was never actually implemented — the function that retrieves a page of objects in the parallel strategy was a stub returning nothing. I need to implement it properly so that listing with parallel workers actually retrieves objects from the bucket.

I also need to rename a few internal helpers to make the code clearer. The function that adjusts start and end offsets relative to a prefix should have a name that reflects this behavior. Similarly, the function that fetches a single page of objects during sequential listing should be renamed to make clear it retrieves one page at a time, and its return values should include an integer count of how many objects were iterated through on that page (regardless of whether they were filtered out). The associated page-size constant for sequential listing should also be renamed to distinguish it from any other page-size constants that might be added to the package later.

Both the sequential and parallel listing strategies should correctly return all objects in a bucket. When the listing operation's context is cancelled before completion, the operation should return an error reflecting the cancellation and no objects should be returned.

When enumerating objects in a large bucket with a prefix filter, the implementation should require more than one batch call to enumerate all matching objects. Individual batches are not required to contain exactly the configured batch size, but the total object count across all batches should be correct.
