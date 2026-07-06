## Description

The knowledge graph visualization currently has no interactive behavior for edges. When a user hovers over or clicks an edge connecting two nodes, nothing happens — there is no tooltip showing which entities are connected or what relationship the edge represents, and the graph does not navigate in response to edge clicks.

## Expected Behavior

- **Hover (enter):** When a user moves their pointer over an edge, a tooltip should appear near the cursor showing the relationship label(s) and the labels of both the source and target nodes. If an edge represents multiple merged relationships, all individual labels should be shown. Hovering over an edge should also visually highlight the source and target nodes.
- **Hover (leave):** When the pointer leaves an edge, the tooltip should be dismissed and the edge and nodes should return to their previous visual state. If a node was already selected before the hover, the selection highlight should be restored.
- **Click:** When a user clicks an edge, the graph should animate to bring the opposite endpoint into view. If the source node is currently selected, the graph should focus the target node; if the target is selected, the graph should focus the source. If nothing is selected, clicking the edge should focus the target node.

## Why This Matters

Edges in the knowledge graph carry semantic meaning — they describe how entities relate to each other. Without any hover or click behavior, users have no easy way to read edge labels or navigate between connected entities. Adding these interactions makes the graph significantly more useful for exploring data relationships.
