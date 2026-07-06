## Description

The library currently supports fetching transaction details, but there is no built-in action or hook for fetching **transaction receipts**. A transaction receipt is the confirmation record produced after a transaction is mined — it contains the execution status (success or failure), gas consumption, emitted event logs, contract address (for deployments), and block placement information.

This is a common need: developers often submit a transaction and then need to verify it succeeded, check how much gas was used, or inspect emitted events. Without a first-class receipt action, they must drop down to lower-level abstractions outside the library.

## Expected Behavior

- A new core action should be available to retrieve a transaction receipt by transaction hash, with optional chain targeting.
- Corresponding query utilities (query key and query options) should be available for use with TanStack Query-style caching patterns.
- A React hook should be available that wraps the action with the library's standard reactive query system.
- The hook should remain idle (not fetch) when no transaction hash is provided, and should automatically begin fetching as soon as a hash becomes available.
- The hook should include the active chain's identifier in its cache key automatically, even when no chain is explicitly specified.
- All new symbols should be accessible via the library's standard public exports.

## Why This Matters

Transaction receipts are essential for confirming that submitted transactions actually succeeded. Many dApp flows — such as showing a success message, revealing a minted NFT, or enabling a next step after a transfer — depend on reading the receipt. Having this as a first-class library feature keeps developers in the library's consistent patterns rather than requiring them to reach for lower-level tooling.
