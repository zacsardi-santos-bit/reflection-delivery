Implement a method to compute a consistent archive point for blockchain state syncing. Ensure that this archive point is deterministically calculated and can be used by all peers to serve the same snapshot for parallel downloads.

*   Implement `txhashset_archive_header` in `chain/src/chain.rs`:
    *   Method signature: `txhashset_archive_header(&self) -> Result<BlockHeader, Error>`
    *   Calculate the archive height by subtracting the state sync threshold from the current head height and rounding down to the nearest multiple of the archive interval.
    *   In AutomatedTesting mode with a chain of 35 blocks, ensure it returns the header at height 10.

*   Define constants and functions in `core/src/global.rs`:
    *   Add `txhashset_archive_interval()` function:
        *   Signature: `txhashset_archive_interval() -> u64`
        *   Return 10 for `AutomatedTesting` chain type and 720 for all other types.
    *   Define `AUTOMATED_TESTING_TXHASHSET_ARCHIVE_INTERVAL`:
        *   Value: `10` (u64)
    *   Define `TXHASHSET_ARCHIVE_INTERVAL`:
        *   Value: `720` (u64, equivalent to 12 hours)
    *   Replace `TESTING_CUT_THROUGH_HORIZON` with:
        *   `AUTOMATED_TESTING_CUT_THROUGH_HORIZON`: Value `20` (u32)
        *   `USER_TESTING_CUT_THROUGH_HORIZON`: Value `70` (u32)
    *   Update `cut_through_horizon()` function:
        *   Return `AUTOMATED_TESTING_CUT_THROUGH_HORIZON` (20) for `AutomatedTesting`
        *   Return `USER_TESTING_CUT_THROUGH_HORIZON` (70) for `UserTesting`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.