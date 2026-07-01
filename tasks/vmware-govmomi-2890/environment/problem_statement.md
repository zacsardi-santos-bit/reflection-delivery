## Description

The virtual machine simulator does not correctly normalize virtual disk capacity when a disk is added with only one of the two capacity fields populated, or when the two fields carry conflicting values. A virtual disk has two capacity representations — one in bytes and one in kilobytes — that are supposed to always be in sync. Currently, if a caller provides only the byte-based capacity, the kilobyte field is not derived from it (and vice versa). Additionally, if a caller provides both fields with inconsistent values, neither field is reconciled.

## Expected Behavior

- When a disk is added with only the byte-based capacity specified, the kilobyte-based capacity should be automatically derived by dividing by 1024.
- When a disk is added with only the kilobyte-based capacity specified, the byte-based capacity should be automatically derived by multiplying by 1024.
- When both fields are specified with the same logical capacity, both should be preserved correctly.
- When both fields are specified but represent different capacities, the byte-based field should take precedence, and the kilobyte-based field should be recalculated accordingly.

## Why This Matters

Callers should not be required to manually keep these two fields consistent. The simulator should handle normalization transparently when a virtual disk device is configured, so that queries on the VM's devices always return both fields in a coherent, mutually consistent state.
