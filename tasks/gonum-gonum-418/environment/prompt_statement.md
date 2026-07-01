I'm working with a graph library that has utility functions for copying graph data from one graph representation to another. The functions are supposed to copy all nodes and edges from a source graph into a destination graph, handling conversions between directed and undirected graph types correctly.

The problem is that the current implementation reuses edge objects from the source graph rather than creating new ones through the destination. This means that when I copy an undirected graph into a directed graph, the result isn't right — it should create edges in both directions in the directed destination (since each undirected edge implies connectivity in both directions), but it doesn't.

Similarly, copying a directed graph into an undirected graph, or copying between two graphs of the same type, should produce a destination graph with the correct nodes and edges. The weighted version of this functionality must also preserve edge weights during the copy.

I'd like these copy functions to work correctly for all combinations: undirected to undirected, undirected to directed, directed to undirected, and directed to directed — for both plain and weighted graphs. Isolated nodes (nodes not connected to any edges) should also be carried over to the destination.
