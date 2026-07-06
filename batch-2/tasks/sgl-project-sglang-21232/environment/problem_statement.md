## Description

The expert load balancing (EPLB) module is missing two foundational algorithms needed for efficient expert routing in distributed mixture-of-experts inference:

1. A **balanced packing algorithm** that distributes experts across processing groups by minimizing the maximum total load (weight) any group must handle. This is a greedy bin-packing approach where heavier experts are spread evenly rather than concentrated together.

2. A **locality-aware dispatch mapping** that, for each GPU in a multi-GPU/multi-node configuration, determines which physical replica of each logical expert that GPU should use. The assignment should prefer locally-hosted replicas first, then same-node replicas, and use seeded randomization to break ties fairly.

## Expected Behavior

- The balanced packing algorithm should distribute items across groups such that no group is overloaded relative to others. For example, items with weights [9, 1, 1, 1] split into 2 groups should produce groups with total weights [10, 2] — not [11, 1].
- The packing must guarantee uniform group sizes (each group gets the same number of items per layer) and produce a compact rank assignment within each group.
- The dispatch mapping must return a complete assignment with no unresolved entries — every logical expert at every layer must map to a valid physical expert ID.
- Both algorithms must be deterministic given the same inputs (with seed for the dispatch mapping).

## Why This Matters

Without these algorithms, the framework cannot efficiently assign experts to GPUs in a load-balanced, locality-aware way. This leads to unnecessary cross-node communication and uneven compute distribution when running large mixture-of-experts models across multiple GPUs and nodes.
