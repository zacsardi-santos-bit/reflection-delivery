Fix the range query cache in the `re_query_cache` crate to ensure it properly invalidates stale entries when data is inserted or modified. Implement necessary methods and update tests to ensure the cache returns up-to-date results consistent with uncached queries.

*   Implement cache invalidation:
    *   Ensure the cache invalidates correctly when new data is inserted or existing data is updated at any timepoint.
    *   Invalidate the entire range cache for timeless data updates.
    *   Evict cache entries at or after the modified timepoint for timeful data updates.
    *   Handle successive updates at the same timepoint to reflect the most recent values.

*   Update methods and interfaces:
    *   Implement `truncate_at_time` method in:
        *   `RangeCache` (location: `crates/re_query_cache/src/range.rs`) with signature `truncate_at_time(&mut self, threshold: TimeInt) -> u64`.
        *   `LatestAtCache` (location: `crates/re_query_cache/src/latest_at.rs`) with signature `truncate_at_time(&mut self, threshold: TimeInt) -> u64`.
        *   `CacheBucket` (location: `crates/re_query_cache/src/cache.rs`) with signature `truncate_at_time(&mut self, threshold: TimeInt) -> u64`.
    *   Implement `dyn_total_size_bytes` method in `ErasedFlatVecDeque` trait (location: `crates/re_query_cache/src/flat_vec_deque.rs`) with signature `dyn_total_size_bytes(&self) -> u64`.
    *   Implement `handle_pending_invalidation` method on `CachesPerArchetype` (location: `crates/re_query_cache/src/cache.rs`) with signature `handle_pending_invalidation(&mut self) -> u64`.

*   Implement `SizeBytes` trait:
    *   For `RangeCache` (location: `crates/re_query_cache/src/range.rs`), compute heap size as the sum of `per_data_time.total_size_bytes` and `timeless.total_size_bytes`.
    *   For `LatestAtCache` (location: `crates/re_query_cache/src/latest_at.rs`), return the internal `total_size_bytes` field.

*   Update tests:
    *   Correct the latest-at invalidation test to use distinct frame numbers: `frame_122` should use frame number 122 and `frame_124` should use frame number 124.

*   Update code for field access:
    *   Remove the public `total_size_bytes` field from `RangeCache` and make it private in `LatestAtCache`.
    *   Update any code in `crates/re_query_cache/src/cache_stats.rs` to use `.total_size_bytes()` via the `SizeBytes` trait.
    *   Modify `upsert_results` calls in the range query macro in `crates/re_query_cache/src/range.rs` to no longer increment `range_cache.total_size_bytes`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.