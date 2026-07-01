Implement a new internal package for GCS object stat caching to replace the current external dependency. This package should provide a stat cache with time-based expiration and LRU eviction, along with a bucket wrapper that utilizes this cache to minimize GCS requests.

*   Define the StatCache interface in `internal/gcloud/gcs/gcscaching/stat_cache.go` with the following methods:
    *   `Insert(o *gcs.Object, expiration time.Time)`
    *   `AddNegativeEntry(name string, expiration time.Time)`
    *   `Erase(name string)`
    *   `LookUp(name string, now time.Time) (hit bool, o *gcs.Object)`
    *   `CheckInvariants()`
*   Implement `NewStatCache(capacity int)` to return a StatCache with LRU eviction, limited to a specified capacity.
*   Ensure `StatCache.Insert` adheres to the "best wins" rule, replacing negative entries with positive ones, and only replacing positive entries if the new entry is more recent.
*   Implement `StatCache.LookUp` to handle cache hits and misses, including expired entries.
*   Implement `StatCache.AddNegativeEntry` to overwrite existing entries with a negative entry.
*   Implement `StatCache.Erase` to remove entries by name.
*   Ensure `StatCache.CheckInvariants` can panic if invariants are violated.
*   Implement LRU eviction when the cache reaches capacity.
*   Implement `NewFastStatBucket` in `internal/gcloud/gcs/gcscaching/fast_stat_bucket.go` to return a `gcs.Bucket` wrapper using the StatCache and TTL.
*   Ensure cache invalidation and insertion logic for `CreateObject`, `CopyObject`, `ComposeObjects`, `StatObject`, `ListObjects`, `UpdateObject`, and `DeleteObject` operations.
*   Ensure `StatObject` respects the `ForceFetchFromGcs` flag for cache bypass.
*   Ensure the package is importable as `github.com/googlecloudplatform/gcsfuse/internal/gcloud/gcs/gcscaching`.
*   Provide a mock implementation `MockStatCache` in `internal/gcloud/gcs/gcscaching/mock_gcscaching/mock_stat_cache.go` with a constructor `NewMockStatCache(c oglemock.Controller, desc string)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.