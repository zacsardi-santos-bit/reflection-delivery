## Description

Snapshot filenames currently encode both a block range and a transaction range, but this coupling is unnecessary. The block range alone is sufficient to uniquely identify a snapshot file, since snapshots are indexed by block position. Including the transaction range in the filename forces callers to look up and supply transaction data just to generate or parse a filename, which adds unnecessary complexity.

## Expected Behavior

- Snapshot filenames should be derived from the block range only, encoding the segment type and the start and end block numbers.
- The method that generates a base snapshot filename should take only the block range — no transaction range parameter.
- The method that generates a filename with compression and filter configuration should also drop the transaction range parameter.
- The method that parses a snapshot filename back into a segment and range should accept a plain string (not a platform-specific path component type), and should return only the segment and block range — without a transaction range in the result.
- The filename parser should return a failure result for filenames that are missing either the start or end of the block range.

## Why This Matters

Removing the transaction range from snapshot filenames simplifies the API for all callers. It eliminates the need to fetch or track transaction range data when all that's needed is a filename for a snapshot segment. This makes the naming convention easier to understand and reduces coupling between the filename format and transaction-level indexing.
