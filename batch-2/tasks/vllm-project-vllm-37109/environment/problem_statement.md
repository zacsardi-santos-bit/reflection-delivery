## Description

The KV offloading layer currently identifies offloaded blocks using the same hash type that the GPU block cache uses internally. This creates an unwanted dependency between the offloading subsystem and the GPU cache, and—more importantly—it makes it impossible to distinguish blocks that belong to different KV cache groups, because only a hash is stored with no group information.

We need a dedicated key type for the offloading system that packages a block's hash together with its cache group index. This lets the offloading manager treat blocks from different groups as distinct entries even when their hashes collide, and it decouples the offloading API from the GPU cache internals.

## Expected Behavior

- A new dedicated offload key type and a corresponding factory function must be introduced and exported from the offloading abstract module.
- All offloading manager operations (store, load, touch, lookup) must accept collections of the new key type.
- The result objects returned by prepare-store must report the blocks selected for storing and the blocks evicted using the new key type (renaming the existing fields accordingly).
- Offloading event objects must similarly report their affected blocks using the new key type.
- The scheduler must expose its per-group block-size configuration through a structured config object rather than as flat top-level properties.

## Why This Matters

Without this change, the offloading system cannot support multiple KV cache groups — it has no way to record which group a stored block belongs to. The refactoring also makes the offloading API self-contained: callers no longer need to import or understand the GPU cache's internal block-hash type.
