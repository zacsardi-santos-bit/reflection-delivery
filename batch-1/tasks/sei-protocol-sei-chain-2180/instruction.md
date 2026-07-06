Implement two new operations in the staking precompile to support creating and editing validators through the EVM. Ensure these operations handle input validation and provide clear error messages for invalid inputs.

*   Implement the `createValidator` method in `precompiles/staking/staking.go` with the following specifications:
    *   Accepts parameters: `pubKeyHex` (string), `moniker` (string), `commissionRate` (string), `commissionMaxRate` (string), `commissionMaxChangeRate` (string), `minSelfDelegation` (*big.Int).
    *   Requires a non-zero ETH value for the initial self-delegation.
    *   Returns `bool true` on success, indicating the validator is created with the specified moniker.
    *   Return errors for:
        *   Invalid public key format: "invalid public key hex format".
        *   Missing funding: "set `value` field to non-zero to send delegate fund".
        *   Unparseable commission values: "invalid commission rate", "invalid commission max rate", "invalid commission max change rate".
        *   Zero self-delegation: "minimum self delegation must be a positive integer: invalid request".
        *   Unlinked EVM address: "address <evmAddress> is not linked".

*   Implement the `editValidator` method in `precompiles/staking/staking.go` with the following specifications:
    *   Accepts parameters: `moniker` (string), `commissionRate` (string), `minSelfDelegation` (*big.Int).
    *   Non-payable method.
    *   Returns `bool true` on success, indicating the validator's moniker is updated.
    *   Return error for non-existent validator: "validator does not exist".
    *   Preserve existing values if `commissionRate` is an empty string or `minSelfDelegation` is zero.
    *   Ensure the operator address and consensus public key remain unchanged after edits.

*   Update the `StakingKeeper` interface in `precompiles/common/expected_keepers.go`:
    *   Add `CreateValidator` and `EditValidator` methods matching the cosmos-sdk staking message server signatures.

*   Define constants in `precompiles/staking/staking.go`:
    *   `const CreateValidatorMethod = "createValidator"`
    *   `const EditValidatorMethod = "editValidator"`

*   Update `precompiles/staking/abi.json` to include:
    *   `createValidator` method as payable with specified inputs and output.
    *   `editValidator` method as non-payable with specified inputs and output.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.