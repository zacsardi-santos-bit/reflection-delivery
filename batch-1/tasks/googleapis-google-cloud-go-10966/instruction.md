Implement the missing parallel work-stealing listing functionality in the fast object-listing library for cloud storage and refactor internal helpers for clarity. Ensure both sequential and parallel listing strategies function correctly and handle context cancellation appropriately.

*   Rename and refactor functions and constants:
    *   Implement `prefixAdjustedOffsets` in `storage/dataflux/fast_list.go` to adjust start and end offsets relative to a prefix. Replace the previous function `updateStartEndOffset`.
    *   Rename the constant `defaultPageSize` to `seqDefaultPageSize` in `storage/dataflux/sequential.go` to specify the maximum number of objects fetched per page during sequential listing.
    *   Rename `doSeqListing` to `listNextPageSequentially` in `storage/dataflux/sequential.go`. Ensure it returns a list of objects, a next-page token, the count of objects iterated, and an error. The page size must not exceed `seqDefaultPageSize`.

*   Implement listing methods:
    *   Implement `(*Lister).sequentialListing(ctx context.Context)` in `storage/dataflux/sequential.go` to perform sequential listing and return all objects, a next-page token, and an error. Ensure the Lister can be constructed with fields `method`, `bucket`, and `query`.
    *   Implement `(*Lister).workstealListing(ctx context.Context)` in `storage/dataflux/worksteal.go` to perform parallel work-stealing listing and return all objects and an error. Ensure the method field of Lister is set to `worksteal`.

*   Ensure correct functionality and error handling:
    *   Ensure `(*Lister).NextBatch` returns an error containing `context.Canceled` and an empty object slice if called with a cancelled context.
    *   Ensure `(*Lister).NextBatch` can be called in a loop to accumulate objects across multiple calls, requiring more than one call to list a bucket containing 17225 objects with a prefix filter.

*   Ensure the range splitter computes correct split points:
    *   Verify that splitting the range from 'aaaaabbcccccc' to 'xxxxyz' into 2 parts produces split points ['b', 'c'].

*   Update the Lister struct and constants:
    *   Ensure the Lister struct has fields `method`, `bucket`, and `query` within the `dataflux` package.
    *   Define constants `sequential` and `worksteal` for listing strategies.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.