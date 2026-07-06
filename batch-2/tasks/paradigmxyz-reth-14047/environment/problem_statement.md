## Description

The engine tree's state caching system stores all account storage entries under a single shared cache with a flat composite key. There is no dedicated type for holding the storage slots of a single account with its own configurable capacity. This makes per-account storage management harder to reason about and to extend.

## Expected Behavior

- A new per-account storage cache type should be introduced in the existing cached-state module of the engine tree.
- The type should be constructable with a configurable maximum number of storage slots it is allowed to hold.
- A freshly constructed cache should report that it currently holds zero slots.
- The type should be accessible from within the crate through the existing cached-state module path.

## Why This Matters

Having a dedicated per-account storage cache type creates a cleaner abstraction for organizing and managing storage slot data at the account level. It enables more efficient invalidation of an entire account's cached storage in one operation, and makes the overall caching hierarchy easier to reason about and test independently.
