I'm working with D2's glob patterns to apply styles to groups of nodes. Right now I can filter by whether a node exists or by its label, but I need two more filter types that don't seem to be supported yet.

The first is a filter based on whether a node is a "leaf" — meaning it has no children. I want to be able to style all container nodes (non-leaves) differently from leaf nodes using a single glob pattern rather than listing them manually. For example, applying a fill color to all nodes that are NOT leaves in a three-level nested hierarchy should style the two container nodes (the outer levels) but skip the deepest node (since it is a leaf).

The second is a filter based on whether a node is "connected" — meaning it participates in at least one edge. I want to apply a style to all nodes that have connections without having to name them explicitly. For example, in a diagram where two nodes are connected by an edge and a third node is isolated, enabling this filter should style the two connected nodes but skip the isolated one.

In both cases, when a node doesn't match the filter condition, it should be left completely untouched — none of the attributes in the glob block should be applied to it. Can these two boolean filter types be added to the glob system?
