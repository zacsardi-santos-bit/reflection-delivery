Implement a multi-chain database coordinator and a persistent head tracker for the supervisor component of a multi-chain system. Ensure operations are routed correctly based on chain identity and that head states are persisted and atomically updated.

*   Implement `ChainsDB` in `op-supervisor/supervisor/backend/db/`:
    *   Route `AddLog` and `Rewind` operations to the correct per-chain `LogStorage` using chain ID.
    *   Return `ErrUnknownChain` for unrecognized chain IDs.
    *   `AddLog` should accept chain ID as the first parameter and delegate to the correct `LogStorage`.
    *   `Rewind` should accept chain ID and head block number, delegating to the correct `LogStorage`.

*   Create `Heads` and `ChainHeads` in `op-supervisor/supervisor/backend/db/heads/`:
    *   Define `ChainHeads` with integer fields for various head pointers.
    *   Implement `Heads` with a `Chains` map, supporting JSON marshaling/unmarshaling.
    *   Provide methods: `Put`, `Get`, and `Copy`.

*   Implement `HeadTracker` in `op-supervisor/supervisor/backend/db/heads/`:
    *   Use `NewHeadTracker` to create/load from a file, starting with empty heads if the file doesn't exist.
    *   `Apply` operations atomically, ensuring no changes on error.
    *   `Current` should return a copy of the current `Heads` state.

*   Move `DB` type to `op-supervisor/supervisor/backend/db/logs/`:
    *   Retain existing API and add `LatestBlockNum` method.
    *   Move error sentinels and interfaces (`Metrics`, `EntryStore`) to this package.

*   Update constructors and interfaces in the source package:
    *   `NewChainProcessor` should accept chain ID as the third parameter.
    *   `DatabaseRewinder.Rewind` should accept chain ID as the first parameter.
    *   `newLogProcessor` should accept chain ID as the first parameter.
    *   `LogStorage.AddLog` should accept chain ID as the first parameter.

*   Ensure `Resume` function in `op-supervisor/supervisor/backend/db/`:
    *   Accepts `LogStorage` and returns only an error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.