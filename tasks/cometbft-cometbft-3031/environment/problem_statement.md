## Description

When querying block results from the RPC endpoint on a node that has been upgraded from an older version of the software, the response either fails or returns incorrect data. The old block processing format did not include an application hash in its responses, and the current code cannot reliably distinguish between data stored in the old format and data in the new format. This causes historical block results to be incorrectly deserialized, resulting in zero-valued or missing fields in the response.

Additionally, the block results RPC response is missing the application hash field entirely, so clients have no way to verify the application state root that was produced when a block was finalized.

## Expected Behavior

- The block results endpoint should return the application hash alongside the other block result fields (transaction results, events, validator updates, consensus parameter updates).
- When historical block data was stored in the old format (before the current block processing model was introduced), the system should detect this automatically and convert the data to the current format, mapping all available fields correctly.
- The detection should be based on whether the application hash field is absent after deserialization — if it is absent, the system should re-attempt deserialization using the legacy format and convert accordingly.
- Events from the old begin-block and end-block phases should be merged into the unified events list, with each event tagged to indicate which phase it originated from.
- Fields that are not present in the old format (such as the application hash) should be left absent rather than causing an error.

## Why This Matters

Nodes that have been running since before the unified block finalization model was introduced hold historical data in an incompatible serialization format. Without proper detection and conversion, operators cannot query block results for those historical heights, which breaks tooling, explorers, and any client that needs to verify historical state. Including the application hash in the response also closes a gap for clients that need to verify application state integrity.
