Implement the necessary changes to ensure correct routing with edge-based contraction hierarchy when using via-points and turn restrictions. Update the utility function for building random graphs to enhance its flexibility and control over randomness.

* Update the `buildRandomGraph` method in `GHUtility`:
    * Accept a `Random` object as the second parameter instead of a long seed, allowing the caller to control the random state.
    * Include a `DecimalEncodedValue` parameter for speed encoding, which may be nullable. If non-null, assign independent random forward and backward speeds to each generated edge using this encoded value.
    * Add a `double pNonZeroLoop` parameter to control the probability of assigning non-zero distances to loop edges, replacing the previous constant value.

* Modify edge-based CH routing (using `EDGE_BASED_2DIR` traversal mode):
    * Ensure the algorithm correctly finds a path through a virtual node when no restrictions block it, visiting all nodes in the correct order and providing an accurate total distance.
    * Ensure the algorithm correctly reports no path exists when turn restrictions make all routes impossible, including those through virtual nodes. Return a path where `isFound()` is false when no valid path exists.
    * Prevent U-turns onto virtual edges when a shortcut passes through a virtual node added by a query. Ensure the routing result respects this constraint.

* Ensure that random routing correctness tests for edge-based traversal mode produce CH routing results that agree with reference Dijkstra results within a weight tolerance of 1.e-1.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.