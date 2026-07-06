Implement a utility for computing hierarchical archive layers in the beacon node's state archiving system. This utility should parse a configuration string of epoch intervals, validate it, and compute the necessary snapshot and diff slots for reconstructing historical states.

*   Implement the `HierarchicalLayers` class in `packages/beacon-node/src/chain/archiveStore/utils/hierarchicalLayers.ts`.
    *   Use a static method `fromString(layers?: string): HierarchicalLayers` to parse a comma-separated string of positive integer epoch intervals in ascending order.
        *   Trim whitespace around commas before parsing.
        *   Throw a `LodestarError` with specific error codes if:
            *   The string is empty: `HierarchicalLayersErrorCode.EmptyEpochs`.
            *   Contains fewer than two layers: `HierarchicalLayersErrorCode.MinLayers`.
            *   Contains non-integer, zero, or negative values: `HierarchicalLayersErrorCode.InvalidLayerEpoch`.
            *   Values are not in strictly ascending order: `HierarchicalLayersErrorCode.InvalidOrder`.
    *   Ensure all errors are instances of `LodestarError` from `@lodestar/utils`, with `getMetadata()` returning an object with the error code.
    *   Implement `toString(): string` to return the original configuration string exactly.
    *   Implement `get totalLayers(): number` to return the total count of layers, including the snapshot layer.
    *   Implement `getArchiveLayers(slot: Slot): Layers` to return an object with:
        *   `snapshotSlot`: the start slot of the most recent snapshot epoch at or before the given slot, or 0 if no snapshot epoch has been reached.
        *   `diffSlots`: an ordered, deduplicated list of diff slots since the last snapshot.
            *   Include the most recent occurrence of each diff-layer interval that falls after the snapshot epoch and at or before the given slot's epoch.
            *   Exclude diff layers that have not fired since the last snapshot.
            *   Order slots from coarser to finer intervals.

*   Define the `Layers` type alias in `packages/beacon-node/src/chain/archiveStore/utils/hierarchicalLayers.ts` with:
    *   `snapshotSlot: Slot`
    *   `diffSlots: Slot[]`

*   Export `HierarchicalLayers` and `Layers` as named exports from `packages/beacon-node/src/chain/archiveStore/utils/hierarchicalLayers.ts`.

*   Define and export the `HierarchicalLayersErrorCode` enum in `packages/beacon-node/src/chain/archiveStore/errors.ts` with members:
    *   `InvalidLayerEpoch`
    *   `EmptyEpochs`
    *   `MinLayers`
    *   `InvalidOrder`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.