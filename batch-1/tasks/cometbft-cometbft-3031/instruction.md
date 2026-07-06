Implement the block results RPC endpoint to correctly handle legacy data formats and include the application hash in responses. Ensure the system can detect and convert old data formats to the current format, merging events appropriately and handling missing fields without errors.

*   Update the block results RPC endpoint:
    *   Include an `AppHash` field in the response, populated from `FinalizeBlockResponse.AppHash`.
    *   Modify `ResultBlockResults` struct in `rpc/core/types/responses.go` to include `AppHash []byte`.
    *   Ensure `BlockResults` method sets `AppHash: results.AppHash` when constructing the response.

*   Enhance data loading and conversion logic:
    *   Modify `LoadFinalizeBlockResponse` in `state/store.go`:
        *   Check if `AppHash` is `nil` after unmarshalling into `FinalizeBlockResponse`.
        *   If `AppHash` is `nil`, re-unmarshal data as `LegacyABCIResponses`.
        *   On legacy unmarshal success, convert using `responseFinalizeBlockFromLegacy`.
        *   On legacy unmarshal failure, return `ErrABCIResponseCorruptedOrSpecChangeForHeight`.

*   Implement legacy data conversion:
    *   Create `responseFinalizeBlockFromLegacy` in `state/store.go`:
        *   Convert `LegacyABCIResponses` to `FinalizeBlockResponse`.
        *   Merge `BeginBlock` and `EndBlock` events into a single `Events` slice.
            *   Append `BeginBlock` events first, each with `{Key:'mode', Value:'BeginBlock'}`.
            *   Append `EndBlock` events next, each with `{Key:'mode', Value:'EndBlock'}`.
        *   Assign `EndBlock.ValidatorUpdates` and `EndBlock.ConsensusParamUpdates` if non-nil.
        *   Set `TxResults` from `DeliverTxs` if non-nil.
        *   Leave `AppHash` as `nil`.

*   Handle error scenarios:
    *   Define `ErrABCIResponseCorruptedOrSpecChangeForHeight` in `state/errors.go`:
        *   Structure: `{ Err error; Height int64 }`.
        *   Implement `Error()` to return a message for the given height.
        *   Implement `Unwrap()` to return the inner `Err`.

*   Ensure data integrity:
    *   When `SaveFinalizeBlockResponse` is called with a `FinalizeBlockResponse` that has a non-nil `AppHash`, ensure `LoadFinalizeBlockResponse` at the same height returns the data unchanged.

*   Update testing setup:
    *   Implement `setupTestCaseWithStore` in `state/state_test.go`:
        *   Set up a test database, state, and state store.
        *   Return a teardown function, the DB, the state, and the state store.
        *   Ensure `setupTestCase` delegates to this helper and returns only the first three values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.