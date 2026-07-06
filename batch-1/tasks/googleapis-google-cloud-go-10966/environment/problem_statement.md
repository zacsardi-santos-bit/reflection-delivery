## Description

The fast parallel object-listing library for cloud storage has an incomplete and partially broken implementation. The work-stealing component that enables high-speed parallel listing across multiple workers was never fully implemented — the core function responsible for fetching each page of objects in the parallel path was a stub that returned nothing. This means that for any workload where the parallel strategy wins the race, no objects are actually returned.

Additionally, several internal helpers have names that don't clearly reflect what they do, making the code harder to read and maintain. Specifically, the function that adjusts start/end offsets relative to a prefix filter, and the function that retrieves a single page of objects during sequential listing, have names that don't describe their purpose well. A related page-size constant has the same naming issue.

## Expected Behavior

- The function that adjusts listing offsets based on a prefix should be renamed to more clearly reflect that it strips or adjusts those offsets relative to the prefix.
- The sequential-listing helper that retrieves one page of objects should be renamed to make clear it fetches exactly one page in sequence, and its return signature should include the count of objects iterated (even if some are skipped due to filtering).
- The page-size constant for sequential listing should be renamed to distinguish it from any other page-size constants in the package.
- The parallel work-stealing listing path should be fully implemented so that it actually retrieves objects from the bucket.
- Both the sequential and parallel listing strategies should be testable against a real storage emulator, and both should correctly return all objects in a bucket.
- When a context is cancelled, the listing operation should return an appropriate error rather than hanging or returning partial results silently.

## Why This Matters

Without a working parallel listing implementation, the fast-list library falls back to sequential listing in all cases, defeating the purpose of the library for large buckets. Developers relying on this library for fast bucket enumeration cannot get the performance benefits it promises.
