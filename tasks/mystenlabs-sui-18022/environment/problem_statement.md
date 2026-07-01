## Description

The consensus engine's test infrastructure needs a way to build randomized directed acyclic graphs (DAGs) to validate correctness properties of the leader commitment algorithm under varied, realistic network conditions.

Currently, test utilities can only build deterministic DAGs, making it difficult to discover edge cases in the commitment logic. There is no mechanism to construct randomized DAGs with configurable probability of linking to leader blocks, nor any way to verify that two validators who receive identical blocks (in different orders) always arrive at the same committed leader sequence.

## Expected Behavior

- A new utility function should be available for building randomized DAGs, taking a random seed (for reproducibility), a percentage controlling how often leader blocks are guaranteed to be linked as ancestors, a total round count, and the shared consensus context.
- When the leader-inclusion percentage is 100%, every round of the resulting DAG should produce a direct commit, with leaders committed in round order.
- When the leader-inclusion percentage is lower (e.g., 50%), two independent authorities that process the same set of blocks — even in different arrival orders — must arrive at exactly the same sequence of committed leaders.
- The layer-building API should be updated to accept a flag indicating whether the current round's leader must be included among the selected ancestors, in addition to the existing seed for randomized ancestor selection.
- The block manager's empty-state check should be accessible to test modules outside its own module.

## Why This Matters

Without randomized DAG construction, tests can only verify the commitment algorithm on hand-crafted topologies, missing the wide variety of real-world network scenarios. These randomized tests provide stronger coverage guarantees and help catch subtle bugs in the leader commitment and skip logic that only emerge under less predictable DAG structures.
