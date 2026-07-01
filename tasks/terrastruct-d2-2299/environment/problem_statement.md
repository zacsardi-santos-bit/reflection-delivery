## Description

D2's glob syntax lets users apply styles or properties to many nodes at once by using wildcard patterns. It already supports filtering glob matches by whether a node exists or by its label value. However, there is currently no way to filter based on **structural position** (whether a node is a leaf or a container) or **topological connectivity** (whether a node participates in any edges).

This means users who want to, for example, style only container nodes or only connected nodes, have to manually enumerate those nodes — which defeats the purpose of glob patterns.

## Expected Behavior

Two new boolean glob filter conditions should be supported:

- A filter based on whether a node is a leaf (has no children). When disabled, it matches only container nodes (non-leaves); when enabled, it matches only leaf nodes.
- A filter based on whether a node participates in any edge. When enabled, it matches only nodes that appear as a source or destination in at least one connection; when disabled, it matches only isolated nodes.

When a filter condition is not met, the node should be skipped and its attributes should remain unchanged (as if the glob was never applied to it).

## Why This Matters

These filters make glob patterns far more expressive for common diagram styling tasks, such as differentiating the appearance of container nodes versus leaf nodes, or highlighting all nodes that are connected in a graph versus those that are isolated.
