Implement caching support for field-existence queries in the index storage layer. Update the postings list cache to handle these queries similarly to regular expression and exact term queries, ensuring efficient retrieval and storage. Modify the read-through segment reader to utilize the cache appropriately.

*   Add a new constant:
    *   Define `PatternTypeField` in `src/dbnode/storage/index/postings_list_cache.go` to represent field-existence queries. Use an empty string for the pattern value internally.

*   Update `PostingsListCache` methods:
    *   Implement `GetField(segmentUUID uuid.UUID, field string) (postings.List, bool)` to retrieve cached postings lists for field queries. Return the list and `true` on a hit, or `nil` and `false` on a miss.
    *   Implement `PutField(segmentUUID uuid.UUID, field string, pl postings.List)` to store postings lists for field queries. Ensure idempotency by avoiding duplicate entries for the same key.

*   Ensure cache management:
    *   Include `PatternTypeField` entries in LRU eviction. Inserting a field entry should affect cache size and can evict older entries. Accessing a field entry should move it to the most-recently-used position.
    *   Remove `PatternTypeField` entries when `PurgeSegment` is called for the corresponding segment UUID, similar to regexp and term entries.

*   Modify `MatchField` method in `read_through_segment.go`:
    *   Implement `MatchField(field []byte) (postings.List, error)` to consult the cache via `GetField` before querying the underlying segment. On a cache miss, call the underlying reader, store the result using `PutField` (if no error), and return the result.
    *   Bypass the cache entirely when the `CacheTerms` option on `ReadThroughSegmentOptions` is `false`, always calling the underlying segment reader directly.
    *   When the `PostingsListCache` pointer is `nil`, bypass the cache and delegate directly to the underlying segment reader without error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.