## Description

Modin currently configures its execution engine and partition format once at import time using plain string variables. This means the engine cannot be changed at runtime — any code that depends on which engine is active has no way to be notified of changes. This makes it impossible to dynamically switch backends or respond to configuration changes after the module has been loaded.

## Expected Behavior

- Introduce a general-purpose publish-subscribe utility that holds a named value, allows the value to be updated, and notifies registered listeners when the value changes. Listeners can subscribe to all changes or register a one-time callback for a specific value.
- Expose the execution engine and partition format settings as instances of this utility at the top level of the package, so other components can subscribe to changes.
- The engine dispatcher should automatically update the active factory whenever the execution engine or partition format setting changes, without requiring a restart or re-import.
- Switching to an unknown engine name should raise a clear error (not silently fail).
- The distributed client variable should be renamed to avoid confusion, and the new publish-subscribe utility should be part of the public package API.

## Why This Matters

Without dynamic reconfiguration, users and library code cannot react to engine changes at runtime. With this change, any component can register a callback to be notified when the engine or partition format changes, enabling more flexible and testable engine lifecycle management. It also corrects a naming inconsistency in the distributed client variable.
