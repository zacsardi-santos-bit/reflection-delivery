Implement a temporary database type to simplify blockchain storage tests by managing the storage lifecycle automatically. Update the helper function for building test blockchains to use this new type, and ensure cleanup failures in tests are reported as errors.

*   Implement `TempDatabase`:
    *   Export `TempDatabase` from `tari_core::test_helpers::blockchain`.
    *   Provide a `new()` constructor for `TempDatabase` that requires no arguments.
    *   Ensure `TempDatabase` implements the `BlockchainBackend` trait.
*   Update `create_new_blockchain_lmdb` function:
    *   Modify the function in `base_layer/core/tests/helpers/sample_blockchains.rs` to accept `validators: Validators<TempDatabase>`.
    *   Remove the filesystem path parameter from its signature.
    *   Ensure it returns `(BlockchainDatabase<TempDatabase>, Vec<ChainBlock>, Vec<Vec<UnblindedOutput>>, ConsensusManager)`.
    *   Construct the database backend using `TempDatabase::new()`.
*   Ensure explicit cleanup in tests:
    *   Use `expect()` with a descriptive message for cleanup failures in tests that still manually remove temporary directories.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.