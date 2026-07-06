## Description

The operator graph transform logic in the data streaming execution layer is broken in two related ways: logical operators maintain reverse dependency tracking that they do not need, and physical operators fail to correctly rewire those reverse dependencies when graph transformations replace nodes.

When a graph transformation replaces one operator with another, the old operator can remain in its former inputs' downstream lists even after it has been removed from the graph. This leads to stale references throughout the operator graph, making resource analysis and execution planning unreliable. Logical operators, which do not need downstream tracking at all, are also burdened with this state unnecessarily.

## Expected Behavior

- Logical operators should not track or expose reverse (downstream) dependency information. No such attribute should be present on logical operator instances before or after a transform.
- Physical operators should correctly track which downstream operators consume their output, and this tracking must remain accurate after any graph transformation.
- When a transform replaces a node in the physical operator graph, the returned graph must have all reverse dependency edges correctly updated: old nodes must be removed from their former inputs' downstream lists, and new nodes must be registered with their actual inputs.
- Transforms that attempt to mutate an operator's input list in-place instead of returning a new node should be rejected with a clear error.

## Why This Matters

Stale reverse dependency edges can cause downstream consumers to be double-counted or missed entirely during resource planning and execution. Keeping unused dependency tracking on logical operators adds complexity and confusion. These bugs make it hard to safely refactor or optimize the execution plan after initial construction.
