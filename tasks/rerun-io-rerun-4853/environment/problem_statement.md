## Description

The range query cache in `re_query_cache` does not properly invalidate stale entries when data is inserted or modified in the underlying data store. This means that after logging new data or updating existing data, subsequent range queries served from the cache can return outdated results instead of the latest values.

## Expected Behavior

- When new data is inserted at any timepoint (past, present, or future relative to the queried range), the cache should detect this and return fresh results on the next query.
- When timeless data is updated, the entire range cache should be invalidated, since timeless data affects all time-range queries.
- When timeful data is updated, all cache entries at or after the modified timepoint should be evicted.
- Successive updates to data at the same timepoint should each cause the cache to reflect the most recent values.
- The cached range query path must always return results that are consistent with the uncached query path.

## Why This Matters

Without proper cache invalidation for range queries, users relying on caching for performance can see stale or incorrect data when their scene or dataset changes over time. This would lead to subtle, hard-to-diagnose rendering bugs where the viewer shows outdated component values even after new data has been logged.

Additionally, the existing latest-at invalidation tests used incorrectly duplicate frame numbers (frames 122 and 124 were both set to frame number 123), meaning those tests were not actually validating distinct-timepoint scenarios as intended. These values should be corrected so the tests cover truly separate timepoints.
