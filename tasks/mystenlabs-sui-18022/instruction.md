Implement a utility function to construct randomized directed acyclic graphs (DAGs) for testing the leader commitment algorithm under varied network conditions. Ensure the function is parameterized by a random seed, a leader inclusion probability, and a number of rounds. Update related components to support this functionality and verify correctness properties.

*   Implement the `create_random_dag` function in `consensus/core/src/test_dag.rs` with the following signature:
    *   `create_random_dag(seed: u64, include_leader_percentage: u64, num_rounds: Round, context: Arc<Context>) -> DagBuilder`
    *   Use a seeded random number generator to ensure reproducibility.
    *   Panic with an assertion failure if `include_leader_percentage` is not within 0 to 100.
    *   For each round, call `layer(r).min_ancestor_links(include_leader, Some(random_num))` on the `DagBuilder`.
        *   `include_leader` is true if the random number is less than or equal to `include_leader_percentage`.

*   Update the `min_ancestor_links` method in `consensus/core/src/test_dag_builder.rs`:
    *   Signature: `min_ancestor_links(self, include_leader: bool, seed: Option<u64>) -> Self`
    *   Ensure the leader block for the ancestor round is included in the ancestors when `include_leader` is true.

*   Modify the `BlockManager` in `consensus/core/src/block_manager.rs`:
    *   Make the `is_empty` method `pub(crate)` to allow access from external test modules.
    *   Signature: `is_empty(&self) -> bool`

*   Ensure the `blocks` field in `DagBuilder` is accessible:
    *   Location: `consensus/core/src/test_dag_builder.rs`
    *   Must be `pub` or `pub(crate)` to allow iteration over all `VerifiedBlock` values.

*   Validate the following correctness properties:
    *   With `include_leader_percentage=100`, `try_decide` from round 0 should return a sequence of `NUM_ROUNDS-2` committed leaders, each as a direct commit.
    *   With `include_leader_percentage=50`, ensure two validators processing the same blocks in different orders produce identical commit sequences. After processing, `BlockManager::is_empty` should return true for both validators.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.