## Description

The mixed-precision training utilities are missing helper functions for navigating the computational graph structure. There is currently no way to look up the index of a specific operation within a block descriptor, and no way to find which downstream operations consume a given output variable. These lookup utilities are needed to support correct ordering and validation of optimization operations during mixed-precision training.

Additionally, the collective distributed optimizer wrapper does not validate configuration conflicts early enough. If a user enables recompute or automatic mixed precision through the distributed strategy but also wraps their base optimizer in an incompatible optimizer type, the system either silently mishandles the configuration or fails with an uninformative error deep in training. The strategy object also lacks formal support for the automatic mixed precision flag.

## Expected Behavior

- A utility function to search a block for a specific operation and return its index (or -1 if not found) should be available in the mixed-precision utilities module.
- A utility function to find all subsequent operations in a graph that consume a specific named variable should be available in the same module.
- The distributed strategy configuration should include an attribute to enable automatic mixed precision mode.
- When creating a collective optimizer with a recompute strategy, if the checkpoint list is not a proper list type, a clear error should be raised immediately at construction time.
- When calling minimize with recompute enabled and an empty checkpoint list, a clear error should be raised before any training proceeds.
- When the base optimizer is already wrapped in a conflicting optimizer (recompute or mixed precision) and the corresponding strategy flag is also set, a clear error should be raised to prevent double-wrapping.

## Why This Matters

These changes prevent silent misconfiguration bugs in distributed mixed-precision and recompute training workflows. Without early validation, users can spend significant time debugging failures that occur deep in the training loop but were caused by a simple configuration mistake at setup time.
