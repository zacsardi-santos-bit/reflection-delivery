Update the monitoring metric in the raftstore layer of TiKV to correctly track the number of regions using the fast-path apply feature, ensuring it counts regions where the feature is enabled. Adjust the metric to reflect region lifecycle events accurately, such as region splits and merges.

*   Rename and invert the existing metric to `RAFT_ENABLE_UNPERSISTED_APPLY_GAUGE` to count regions with the feature enabled.
    *   Ensure it is publicly exported from `raftstore::store::metrics`.
*   Implement behavior for `RAFT_ENABLE_UNPERSISTED_APPLY_GAUGE`:
    *   Increment when a region enables the unpersisted apply feature.
    *   Decrement when a region disables the feature or is destroyed.
*   Ensure the metric reflects the correct count of regions using the feature:
    *   After initial writes to a single region, the gauge should reflect 1.
    *   After a region split, the gauge should increment to reflect both sub-regions (expected to be 2).
    *   After a region merge, the gauge should decrement to reflect the reduced number of active regions (expected to be 1).
*   Ensure the gauge is accessible and can be referenced from integration tests.
*   Update the `disable_apply_unpersisted_log` function in `components/raftstore/src/store/peer.rs`:
    *   Signature: `pub fn disable_apply_unpersisted_log(&mut self, min_enable_index: u64)`
    *   Ensure it decrements `RAFT_ENABLE_UNPERSISTED_APPLY_GAUGE` if the feature was previously enabled.
    *   Make it public to allow calls from external peer FSM code.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.