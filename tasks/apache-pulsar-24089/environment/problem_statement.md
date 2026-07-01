## Description

The logic for estimating how many ledger entries can be read within a given byte budget is currently embedded inside a cursor implementation and is difficult to test independently. Worse, it crashes when used with read-only cursors because it unconditionally accesses the currently active write ledger — which does not exist in a read-only managed ledger context, causing an unexpected runtime failure.

This means that any attempt to perform a size-bounded asynchronous read on a read-only cursor results in an unexpected exception rather than a successful read.

## Expected Behavior

- The entry-count estimation logic should be extracted into a dedicated utility component that can be tested directly with ledger metadata maps, without needing a full managed ledger instance.
- The utility should correctly handle:
  - A "beginning of time" read position (before any stored ledger), starting estimation from the first available ledger.
  - A "latest" read position (past all stored ledgers), estimating from only the last active ledger.
  - A missing active ledger ID (the read-only case), using the last entry in the ledger metadata map as the active ledger.
  - Empty ledgers, which should be skipped; when only an empty ledger is present, a default estimated entry size should be used.
  - Extremely small byte budgets that can still accommodate at least one entry — the result should be at least 1.
  - An explicit upper bound on the number of entries to return.
  - When the maximum byte size is the largest possible value, return the maximum entry count immediately.
  - A zero or negative byte budget, returning 0.
- A read-only cursor must be able to complete size-bounded asynchronous reads without crashing, returning the correct number of entries.

## Why This Matters

Without this fix, operators running read-only cursors against managed ledgers cannot use the size-bounded read API at all. Extracting the estimation logic also makes the behaviour verifiable in isolation, reducing the risk of future regressions.
