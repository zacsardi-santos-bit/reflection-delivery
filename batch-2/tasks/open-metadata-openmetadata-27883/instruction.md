I'm working on adding interactive edge behavior to a knowledge graph visualization.

*   The setupGraphEventHandlers function must register exactly 8 G6 event handlers: node:click, node:dblclick, node:pointerover, node:pointerleave, canvas:click, edge:pointerover, edge:pointerleave, and edge:click.

*   The context object passed to setupGraphEventHandlers must accept two new properties: setEdgeTooltip (a callback function) and canvasRef (a React ref object).

*   On edge:pointerover, setupGraphEventHandlers must call setEdgeTooltip with an object containing: x and y (from the event's client coordinates), edgeId (the hovered edge's id), labels (edge.data.mergedLabels array if present, otherwise [edge.data.label]), sourceLabel (the label of the source node from graphDataNodes), and targetLabel (the label of the target node from graphDataNodes).

*   On edge:pointerover, the handler must also update both the source and target nodes' data (via graph.updateNodeData) to apply a highlight effect.

*   On edge:pointerleave, setupGraphEventHandlers must call setEdgeTooltip(null) to dismiss the tooltip.

*   On edge:pointerleave, the handler must reset the hovered edge's visual style by calling graph.updateEdgeData with an update that includes the previously-hovered edge's id.

*   On edge:pointerleave, if a node is currently selected (tracked via selectedNodeIdRef), the handler must re-apply the path highlight by calling graph.updateNodeData.

*   On edge:click, the handler must call graph.focusElement with a numeric duration option. It focuses the target node when the source is selected, focuses the source node when the target is selected, and defaults to focusing the target node when nothing is selected.

*   The graph.focusElement call on edge:click must include a duration option with a numeric value, e.g. graph.focusElement(nodeId, { duration: <number> }).


*   Interface details: Type: Function
Name: setupGraphEventHandlers
Location: openmetadata-ui/src/main/resources/ui/src/utils/KnowledgeGraph.utils.ts
Signature: setupGraphEventHandlers(ctx: GraphInteractionCtx) -> void
Description: Registers all G6 graph event handlers on the graph instance contained in ctx. After this change, the function must register exactly 8 event handlers: node:click, node:dblclick, node:pointerover, node:pointerleave, canvas:click, edge:pointerover, edge:pointerleave, and edge:click. The ctx parameter is of type GraphInteractionCtx which must now include setEdgeTooltip and canvasRef in addition to existing fields.

Type: Interface/Type
Name: GraphInteractionCtx
Location: openmetadata-ui/src/main/resources/ui/src/components/KnowledgeGraph/KnowledgeGraph.interface.ts
Description: The context object passed to setupGraphEventHandlers. Must be extended with two new fields:
- setEdgeTooltip: (state: EdgeTooltipState | null) => void
- canvasRef: React.RefObject<HTMLDivElement | null>

Type: Interface
Name: EdgeTooltipState
Location: openmetadata-ui/src/main/resources/ui/src/components/KnowledgeGraph/KnowledgeGraph.interface.ts
Description: Shape of the object passed to setEdgeTooltip when an edge is hovered. All fields are required:
- x: number (client.x from the pointer event)
- y: number (client.y from the pointer event)
- edgeId: string (the id of the hovered edge)
- labels: string[] — uses edge.data.mergedLabels when present; otherwise [edge.data.label]
- sourceLabel: string (the label of the source node from graphDataNodes)
- targetLabel: string (the label of the target node from graphDataNodes)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.