## Description

The beacon node's state archiving system needs a configurable hierarchical layer strategy for storing and reconstructing historical states efficiently. Currently there is no utility for defining how states are stored across multiple tiers — some as full snapshots at wide epoch intervals, others as incremental diffs at finer intervals — or for computing exactly which layers are needed to reconstruct state at any given slot.

We need a new utility that:

- Accepts a configuration expressed as a comma-separated list of epoch intervals in ascending order, where the **last (largest) number** defines how often full snapshots are stored and the **earlier numbers** define progressively finer tiers of incremental diffs.
- Given any slot number, computes which snapshot slot and which diff slots are needed to reconstruct state at that slot.
- Validates the configuration string and rejects it with descriptive error codes if it is empty, contains fewer than two layers, contains non-positive or non-integer intervals, or lists intervals out of ascending order.

## Expected Behavior

- Parsing a valid comma-separated string (e.g., four ascending positive integers) returns an object that reports the total number of layers and can reproduce the original string.
- Querying for the archive layers at a slot returns a snapshot slot (the start of the most recent snapshot epoch at or before that slot, or genesis) and a deduplicated, ordered list of diff slots (the most recent applicable diff slot from each diff tier since the last snapshot).
- Parsing an empty string, a single-layer string, a string with negative/zero/fractional values, or a string whose values are not in strictly ascending order each raises a structured error with a specific error code.

## Why This Matters

Without this utility, the beacon node has no way to determine the minimal set of archived states required to reconstruct any historical state when running in a differential backup mode. This is a prerequisite for any multi-layer archiving feature that stores full snapshots at wide intervals and cheap diffs at narrow intervals.
