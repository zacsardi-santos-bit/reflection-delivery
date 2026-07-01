Add a field to the `BlockExecutedData` struct to capture the write sets produced by each transaction during block execution. Ensure that this field is initialized correctly when a default instance is constructed.

*   Update the `BlockExecutedData` struct in `executor/src/block_executor.rs`:
    *   Add a public field named `write_sets` of type `Vec<WriteSet>`.
    *   Ensure the `write_sets` field is initialized as an empty vector in the `Default` implementation.
*   Ensure `BlockExecutedData` remains publicly accessible via the `starcoin_executor` crate.
*   Confirm the `write_sets` field is named exactly `write_sets` and supports the `.len()` method.
*   Verify that `BlockExecutedData::default().write_sets.len()` returns 0.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.