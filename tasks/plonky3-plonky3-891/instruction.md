Rename types, traits, and functions in the FRI protocol library to use "parameters" instead of "config" to accurately reflect their role as cryptographic parameters. Update all relevant parts of the codebase to ensure consistency and clarity.

*   Rename the `FriConfig` struct to `FriParameters`.
    *   Ensure it retains all existing fields: `log_blowup`, `log_final_poly_len`, `num_queries`, `proof_of_work_bits`, `mmcs`.
    *   Export `FriParameters` from the `p3_fri` crate.

*   Rename the `FriGenericConfig` trait to `FriFoldingStrategy`.
    *   Retain all existing associated types and methods.
    *   Export `FriFoldingStrategy` from the `p3_fri` crate.

*   Rename the `TwoAdicFriGenericConfig` struct to `TwoAdicFriFolding`.
    *   Maintain its structure as a tuple struct wrapping `PhantomData`.
    *   Export `TwoAdicFriFolding` from the `p3_fri` crate.

*   Rename helper functions for creating FRI parameters:
    *   `create_test_fri_config` to `create_test_fri_params`.
        *   Keep the signature: `(mmcs: Mmcs, log_final_poly_len: usize) -> FriParameters<Mmcs>`.
        *   Export from the `p3_fri` crate.
    *   `create_test_fri_config_zk` to `create_test_fri_params_zk`.
        *   Keep the signature: `(mmcs: Mmcs) -> FriParameters<Mmcs>`.
        *   Export from the `p3_fri` crate.

*   Update the `CirclePcs` struct in `circle/src/pcs.rs`:
    *   Rename the field `fri_config` to `fri_params`.
    *   Change the field type from `FriConfig<FriMmcs>` to `FriParameters<FriMmcs>`.
    *   Update all internal usages within `CirclePcs`.

*   Modify the `prover::prove` and `verifier::verify` functions in the `fri` crate:
    *   Accept `FriParameters` instead of `FriConfig` as the `config/params` argument.
    *   Ensure `TwoAdicFriFolding` is usable as the folding strategy argument.

*   Update constructors:
    *   `TwoAdicFriPcs::new` to accept `FriParameters` instead of `FriConfig`.
    *   `HidingFriPcs::new` to accept `FriParameters`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.