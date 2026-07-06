Implement the required changes to the list component to ensure that all items, including those marked as "disabled," are fully included in drag-and-drop operations. Remove the feature that allows selective marking of items as non-draggable.

*   Ensure all rows in a list with drag-and-drop enabled have the draggable attribute set to 'true', regardless of their presence in the disabledKeys list.
*   Include all selected rows in the drag data transfer items when a drag is initiated, even if their keys are in disabledKeys.
*   Update event callbacks:
    *   `onDragStart` must receive a keys set with all selected row keys, including those in disabledKeys.
    *   `onDragMove` must receive a keys set with all selected row keys, including those in disabledKeys.
    *   `onDrop` must receive an items array including all dragged rows, maintaining the selection order, even if their keys are in disabledKeys.
    *   `onDragEnd` must receive a keys set with all selected row keys, including those in disabledKeys.
*   Ensure that when a single disabled row is dragged, its data is included in the drag, and `onDragStart` fires exactly once.
*   Make the drag handle button visible and set its draggable attribute to true for disabled rows on keyboard focus, pointer press, and pointer hover.
*   Update the accessibility aria-label on drag handle buttons to reflect the total count of all selected items, including those in disabledKeys.
*   Remove the per-item drag eligibility callback (previously allowsDraggingItem) from the drag hook options, ensuring all items in a draggable list are draggable.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.