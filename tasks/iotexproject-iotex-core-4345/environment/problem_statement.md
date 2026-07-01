## Description

The peer-to-peer networking library used by this project has undergone a significant restructuring. The peer identity utilities were previously distributed as a separate standalone package, but have now been consolidated into the main library under a new package path. Along with the package reorganization, the method for converting a peer identifier to its string representation was renamed to align with standard Go naming conventions.

The codebase currently references the old standalone package path and the deprecated method name throughout many files — including production code in the node information manager, p2p agent, block syncer, chain service, dispatcher, and message batcher — as well as in generated mock files. These references need to be updated to use the new library structure.

## Expected Behavior

- All files that import the peer library must use the new consolidated package path
- All code that converts peer identifiers to human-readable strings must use the standard string conversion method instead of the deprecated one
- The node information broadcast mechanism must report peer identifiers using the updated string representation, so that the stored peer ID in node info matches the standard string form
- The p2p agent must consistently use the updated string method when capturing and comparing source peer identifiers in both broadcast and unicast message handling
- The module dependency manifest must be updated to reference the new library package and version

## Why This Matters

Without this migration, the codebase fails to compile against the current version of the networking library. Additionally, the deprecated string conversion method has been removed from the new library, so any code that still calls it will break. Updating to the new import path and string conversion method ensures continued compatibility and consistent peer ID representation across the entire node.
