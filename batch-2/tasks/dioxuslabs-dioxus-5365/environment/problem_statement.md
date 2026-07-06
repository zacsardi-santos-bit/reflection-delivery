## Description

The store subscription system has a propagation bug: when a component subscribes to a parent-level store value and code later writes only to a nested child field, the parent component is never notified and does not re-render. This causes the UI to go stale when a deeply nested mutation occurs.

## Expected Behavior

- When a write happens to a nested field in the store, any component that subscribed to a parent or ancestor value must be re-rendered, because the parent value has effectively changed.
- This propagation should work at any nesting depth — a grandparent subscriber must be notified when a grandchild field is written.
- When a nested field is written, components subscribed to unrelated sibling fields must NOT be re-rendered.
- For collection types, writing to a specific element must not notify whole-collection subscribers; only an explicit push or full replacement should mark the whole collection dirty.
- When traversing subscribers of a child store lens, ancestor deep subscriptions must be surfaced and must be removable through that child's subscriber handle.
- Notifications must not fire more than once per subscriber even when a write matches multiple criteria (e.g. direct path and ancestor deep subscription simultaneously).

## Why This Matters

Without this fix, parent-level readers are silently ignored when a child field changes via a lens. Components show stale data and can only be forced to update by writing at the exact path they subscribed to, making fine-grained store mutations unreliable for any component reading an aggregate view.
