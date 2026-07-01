## Description

The ACL resolution system currently stores all plugin global scope permissions in a single flat structure with no plugin attribution. When the resolved permissions are inspected, the global scope appears as one merged collection — making it impossible to determine which plugin a given scope entry came from, and causing each plugin to receive a combined scope from all plugins rather than its own.

## Expected Behavior

- When ACL is resolved and no plugins have global scope permissions, the global scope should appear as an empty collection rather than an empty flat scope structure.
- When one or more plugins have global scope permissions, the resolved global scope should be organized as a per-plugin map where each plugin's entries are grouped under its own name.
- Plugins with both allow and deny global scope entries should have those entries correctly separated and stored together under their own key.

## Why This Matters

Plugin isolation requires that each plugin only operates on its own set of globally-defined permissions, not a merged union of all plugins' permissions. Without proper per-plugin attribution in the resolved global scope, it is impossible for the runtime to deliver the correct scope to each plugin. This change is foundational for correct per-plugin global scope delivery at runtime.
