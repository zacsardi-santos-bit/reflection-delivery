## Description

The graph package provides utility functions for copying graph data from one graph to another. However, the current implementation has a bug where edges are taken directly from the source graph and set on the destination, rather than creating new edges belonging to the destination. This causes problems when copying between different graph types (e.g., undirected to directed), because the edge objects from the source graph do not belong to the destination graph's type system.

## Expected Behavior

- When copying a graph, the destination should receive newly constructed edges that it owns, not references to the source graph's edge objects.
- Copying an undirected graph into a directed graph should produce edges in both directions in the destination (since every undirected edge implies traversal in either direction).
- Copying a directed graph into an undirected graph should produce the appropriate undirected edges in the destination.
- Copying between graphs of the same type (directed-to-directed or undirected-to-undirected) should produce an identical copy in the destination.
- All of the above behaviors must also work correctly for weighted graphs, with edge weights preserved in the destination.
- Isolated nodes (nodes with no edges) must also be copied into the destination.

## Why This Matters

Users who work with graph algorithms often need to convert graph representations or make copies for mutation without affecting the original. These copy functions are essential for interoperability between directed and undirected representations. The current implementation silently produces incorrect results when copying between different graph orientations, which can lead to subtle bugs in downstream algorithms.
