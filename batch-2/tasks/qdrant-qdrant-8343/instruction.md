I'm working on the deduplication logic in our vector database's segment holder, and I've discovered that it doesn't correctly handle records that are in a "pending" or deferred state.

*   The `empty_segment_with_deferred` function must be added to `lib/shard/src/fixtures.rs` and accept a path and a `deferred_internal_id` (u32). It must return a `Segment` where points that receive an internal ID >= `deferred_internal_id` are stored in deferred state. The first `deferred_internal_id` points inserted are non-deferred; all subsequent insertions are deferred.

*   When `point_is_deferred` is called on a segment returned by `empty_segment_with_deferred(path, 3)`, points inserted as the 1st, 2nd, and 3rd point (internal IDs 0, 1, 2) must return false (non-deferred), while points inserted as the 4th point onward (internal IDs >= 3) must return true (deferred).

*   The `find_duplicated_points` method on `SegmentHolder` must never include deferred copies of a point in the removal set, regardless of their version relative to other copies.

*   When a point has multiple deferred copies across different segments, `find_duplicated_points` must include all but exactly one of those deferred copies in the removal set.

*   When a point appears in multiple segments and the highest version exists as a non-deferred copy in at least one segment, `find_duplicated_points` must include all lower-version non-deferred copies in the removal set, while leaving all deferred copies out of the removal set.

*   When a point appears in multiple segments and the highest version exists ONLY in deferred form (no non-deferred copy has the latest version), `find_duplicated_points` must NOT add any non-deferred copy to the removal set — all non-deferred copies must be preserved.

*   After running the full deduplication operation using the segment holder, the total number of points actually removed must equal the total number of point removals returned by `find_duplicated_points`.

*   After deduplication, a point whose older-version non-deferred copy was marked for removal must no longer exist in the segment it was removed from, while it must still exist in the segment holding the winning (latest-version, non-deferred) copy.

*   After deduplication, when neither the non-deferred nor the deferred copy of a point was marked for removal (because the latest version is deferred-only), both copies must still be present in their respective segments.


*   Interface details: Type: Function
Name: empty_segment_with_deferred
Location: lib/shard/src/fixtures.rs
Signature: empty_segment_with_deferred(path: &Path, deferred_internal_id: u32) -> Segment
Description: Creates an empty appendable segment configured so that points assigned an internal ID >= deferred_internal_id are stored in deferred (not-yet-indexed) state. The first deferred_internal_id points inserted will be non-deferred; all subsequently inserted points (i.e., those receiving internal ID >= deferred_internal_id) will be deferred. This function must be publicly accessible as `crate::fixtures::empty_segment_with_deferred`.

Type: Method (updated behavior on existing struct)
Name: find_duplicated_points (on SegmentHolder)
Location: lib/shard/src/segment_holder/mod.rs
Signature: find_duplicated_points(&self) -> HashMap<SegmentId, Vec<PointIdType>>
Description: Returns a map from segment ID to list of point IDs that should be removed from that segment as duplicates. The deduplication logic must be deferred-aware according to these rules:
  1. Deferred copies of a point are NEVER included in the removal set.
  2. Among multiple deferred copies of the same point across segments, all but one are added to the removal set (one deferred copy is always kept).
  3. When the highest version of a point has at least one non-deferred copy: older non-deferred copies are included in the removal set; deferred copies at any version are not included.
  4. When the highest version of a point is deferred-only (no non-deferred copy at the highest version exists): ALL non-deferred copies are preserved (none are added to the removal set).
  5. At least one copy of every point must always remain after deduplication.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.