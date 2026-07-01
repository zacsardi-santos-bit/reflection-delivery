## Description

Textual's reactive system currently allows automatic repaints or layout recalculations when a reactive attribute changes, but it does not support automatically rebuilding a widget's entire child hierarchy in response to state changes. This forces developers to write separate watcher methods that manually update individual child widgets, duplicating logic that already exists in their child-building methods.

A new "recompose" mode is needed: when a reactive attribute is declared with this mode enabled, any change to that attribute should trigger the widget to discard all its current children and rebuild them from scratch. This would work seamlessly alongside the existing data binding mechanism.

## Expected Behavior

- Reactive attributes should support a flag that causes the owning widget to fully recompose (rebuild all children) when the attribute value changes.
- The widget's refresh mechanism should also accept a recompose flag, allowing callers to explicitly trigger a recomposition.
- Recomposing should remove the old children and mount the new ones atomically, so the UI doesn't flicker.
- Recomposing should work correctly when combined with data binding between parent and child widgets.

## Why This Matters

Currently, if a widget's child structure depends on a reactive value, developers must implement a watcher that manually updates or replaces child widgets. With recompose support, the same child-building method can serve both initial mount and subsequent updates, greatly simplifying reactive UI code.
