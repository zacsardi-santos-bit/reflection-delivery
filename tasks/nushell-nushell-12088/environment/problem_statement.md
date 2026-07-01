## Description

The plugin custom value type in nushell's plugin interface currently exposes its internal storage fields directly as public struct members. This makes the API fragile — any addition of new fields requires updating all construction sites that use named-field syntax, and there is no way to enforce invariants or extend behavior without breaking changes.

We need to:
1. Encapsulate the internal fields behind getter methods and a proper constructor
2. Add a "notify on drop" capability so plugins can be informed when one of their custom values is cleaned up

## Expected Behavior

- Plugin custom value instances should be constructed via a dedicated constructor that accepts the type name, binary payload, a boolean flag for drop notification, and an optional source identifier.
- The type name, binary payload, and source should be accessible through getter methods rather than direct field access.
- A new getter should indicate whether the plugin wants to be notified when a value is dropped.
- When a plugin marks a custom value as "notify on drop," the plugin should receive a notification (or print a message) when that value goes out of scope — and this should happen exactly once even if the value was shared.
- Plugin custom values should support cell path navigation (both by integer index and by string key), sorting, and appending via standard operators.

## Why This Matters

This encapsulation makes it possible to evolve the internal representation of plugin custom values without breaking the plugin protocol. The drop notification feature enables plugins to implement cleanup logic or lifecycle callbacks when custom values are garbage collected, which opens the door to more sophisticated resource management in plugins.
