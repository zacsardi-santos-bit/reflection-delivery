I'd like to add column resizing to the deployments data table so users can adjust column widths to better fit their content.

*   The DataTable component must render a resize handle element with data-testid='column-resize-handle-{column.id}' inside each resizable column's header when column resizing is enabled on the table (enableColumnResizing: true) and the column's getCanResize() returns true.

*   The DataTable component must NOT render a resize handle for columns that have enableResizing set to false. No element matching data-testid='column-resize-handle-{column.id}' should be present for non-resizable columns.

*   When column resizing is not enabled on the table (enableColumnResizing is not set to true), the DataTable component must render no resize handles at all — no elements with data-testid matching the pattern 'column-resize-handle-*'.

*   When the resize handle is dragged (mouseDown on the handle, then mouseMove on the document, then mouseUp), the column header element must have its CSS width style updated to reflect the new pixel width determined by the drag. For example, with a column initially 200px wide, dragging from clientX 200 to clientX 280 must result in the column header having width: 280px.

*   When the resize handle is double-clicked, the column width must reset to its originally defined size value from the column definition. For example, a column defined with size: 200 that was resized to 320px must return to width: 200px after a double-click.

*   The DeploymentsDataTable component must have column resizing enabled for the 'name' column and must render a resize handle with data-testid='column-resize-handle-name'.

*   The DeploymentsDataTable component must have the 'actions' column configured with enableResizing: false, so no resize handle with data-testid='column-resize-handle-actions' is rendered.

*   The DeploymentsDataTable component must persist column sizing state to localStorage under the key 'deployments-table-column-sizing' as a JSON-serialized object mapping column IDs to pixel widths. After dragging the 'name' column resize handle from one position to another, the stored value must reflect the new width (e.g., { name: 360 }).

*   The DeploymentsDataTable component must read column sizing state from localStorage under the key 'deployments-table-column-sizing' on mount and apply those widths to the corresponding column headers. For example, if { name: 420 } is stored, the 'Deployment' column header must have CSS width: 420px.


*   Interface details: Type: Component
Name: DataTable
Location: ui-v2/src/components/ui/data-table.tsx
Description: Generic data table component. When the table passed as a prop has enableColumnResizing set to true, the component must render a resize handle element inside the header cell of each resizable column. The resize handle must have data-testid="column-resize-handle-{column.id}". Columns with enableResizing: false must not receive a resize handle. When enableColumnResizing is not set to true, no resize handles are rendered at all. Dragging the handle updates the column header width in real-time. Double-clicking the handle resets the column to its defined default size.

Type: Component
Name: DeploymentsDataTable
Location: ui-v2/src/components/deployments/data-table/index.tsx
Description: Deployments-specific data table built on top of DataTable. Must enable column resizing for the 'name' column (showing a resize handle with data-testid="column-resize-handle-name") and disable resizing for the 'actions' column (no resize handle rendered). Must persist column sizing state to localStorage under the key "deployments-table-column-sizing" as a JSON object of { [columnId]: pixelWidth }. On mount, must read from that key and apply stored widths to column headers. The localStorage key name is exactly: "deployments-table-column-sizing".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.