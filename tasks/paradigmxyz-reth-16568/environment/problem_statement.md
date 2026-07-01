## Description

Currently, the database provider does not expose a way to directly retrieve uncle (ommer) block headers for a given block. There is no dedicated provider trait for fetching ommers, and the database provider does not implement any such functionality. As a result, any code that needs to look up uncle headers from the database provider cannot do so through a clean, stable API.

## Expected Behavior

- There should be a dedicated trait for fetching ommer headers, so components that need uncle data can depend on a narrow, clear interface.
- The database provider should implement this trait, allowing callers to retrieve ommer headers by block number or block hash.
- Calling the ommer retrieval method on the database provider with a valid block number should succeed and return a result (possibly empty if the block has no uncles or is past the Merge).

## Why This Matters

Without this capability, retrieving uncle headers requires loading an entire block and extracting the ommers field manually — which is heavier than needed. A dedicated provider trait and implementation makes it possible for consumers to query ommers directly and efficiently through the standard provider abstraction, consistent with how other block data (headers, receipts, transactions) is accessed.
