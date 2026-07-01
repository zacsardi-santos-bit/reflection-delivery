## Description

When using a list component with drag-and-drop enabled, items that are marked as "disabled" are currently excluded from drag operations. If a user selects multiple items — some disabled, some not — and begins dragging, the disabled items are silently dropped from the drag data. The drag handle button is also hidden for disabled items, preventing users from visually initiating a drag on them.

Additionally, there was previously an option to supply a per-item callback to mark specific items as non-draggable. This created an inconsistency where some selected items would be excluded from the drag payload without any obvious indication to the user.

## Expected Behavior

- All rows in a list with drag enabled should show the draggable indicator, including rows marked as disabled.
- When a user selects multiple rows and initiates a drag, all selected rows — including disabled ones — should be included in the drag data.
- The drag lifecycle events (drag start, drag move, drag end) should report all selected row identifiers including disabled rows.
- The drop event should receive all dragged items, with no silent exclusions.
- The drag handle should appear on hover, press, and keyboard focus for disabled rows, just as it does for non-disabled rows.
- The accessibility label on drag handles should accurately report the number of selected items being dragged, counting disabled items as well.
- The mechanism for selectively marking individual items as non-draggable should be removed — if a list supports drag-and-drop, all items in it are draggable.

## Why This Matters

Users selecting multiple items expect all of them to be dragged together. Silently excluding some items is confusing and leads to unexpected data loss during drag-and-drop operations. Accessibility users relying on announced counts also get incorrect information when disabled items are excluded from the count.
