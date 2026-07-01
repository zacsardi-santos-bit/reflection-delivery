## Description

The system currently has no structured way to manage the dependency relationships between events. When an event is enabled, all of the events it depends on must also be enabled — and when an event is disabled, its dependencies should be cleaned up automatically, but only if no other active event still requires them. Without a dedicated management component, this logic is scattered and prone to errors such as disabling a shared dependency that another event still needs.

## Expected Behavior

- When an event is selected, all of its transitive dependency events should be automatically tracked.
- Each dependency node should know which events depend on it, so shared dependencies are handled correctly.
- When an event is forcibly removed, all events that depended on it should also be removed, and any dependency events that are no longer referenced should be cleaned up.
- When an event is "unselected" (soft removal), it should only be removed if nothing else still depends on it. Its unreferenced dependencies should be cleaned up, but shared dependencies must remain.
- Observers should be able to subscribe to addition and removal notifications, receiving the affected event node each time one is added or removed.

## Why This Matters

Managing event dependencies as a simple map fails when multiple events share overlapping dependencies. A dedicated dependency tree provides correct reference counting and cascading add/remove behavior, enabling the broader system to react reliably when the set of active events changes.
