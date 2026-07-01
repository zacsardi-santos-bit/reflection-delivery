## Description

The build scheduler can merge edges between different build graph vertices that share the same cache key, as a performance optimization. However, under certain graph configurations, this merging process can inadvertently create cycles in what should remain an acyclic directed graph, causing builds to fail or hang indefinitely.

## Expected Behavior

- When two different vertices in the build graph have the same cache key and their edges would form a cycle when merged (depending on input ordering), the scheduler should detect the potential cycle and avoid creating it, completing the build successfully.
- When a vertex has been merged multiple times through different ownership chains, and a later merge through one of those chains would introduce a cycle, the scheduler should still handle the situation correctly and complete the build.
- In both cases, the build should produce the correct computed result — no errors should be returned.

## Why This Matters

This bug can silently cause builds to fail or deadlock under specific graph topologies that arise in practice. Because the failure depends on the order in which vertices are processed, it may be non-deterministic and difficult to reproduce consistently. Fixing it ensures the scheduler reliably handles edge merging for all valid acyclic build graphs, even when identical cache keys are involved.
