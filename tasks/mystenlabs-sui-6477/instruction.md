Implement a new RPC endpoint to retrieve the current system state on the Sui blockchain and ensure all address values are serialized with a "0x" prefix. This will facilitate easier access to validator and staking information and improve interoperability with existing tools.

*   Ensure SuiAddress values serialize to YAML and JSON as quoted strings with a '0x' prefix.
    *   Applies to all occurrences of SuiAddress in serialized output, including sui_address in ValidatorMetadata, validator_address in StakingPool, and sui_address in next_epoch_validators.

*   Add a new RPC method named 'sui_getSuiSystemState' to the Full Node API.
    *   Accepts no parameters and returns a required SuiSystemState object.
    *   Implement in `crates/sui-json-rpc/src/read_api.rs` and register with method name "getSuiSystemState" in the "sui" namespace.

*   Update the OpenRPC specification to include:
    *   Balance schema with a required 'value' property (integer/uint64).
    *   Supply schema with the same structure as Balance.
    *   UID schema with a required 'id' property referencing ObjectID.
    *   SystemParameters schema with required uint64 fields: 'max_validator_candidate_count', 'min_validator_stake', 'storage_gas_price'.
    *   PendingDelegationEntry schema with required fields: 'delegator' (SuiAddress), 'sui_amount' (uint64).
    *   PendingWithdrawEntry schema with required fields: 'delegator' (SuiAddress), 'principal_withdraw_amount' (uint64), 'withdrawn_pool_tokens' (Balance).
    *   StakingPool schema with required fields: 'delegation_token_supply' (Supply), 'pending_delegations' (array of PendingDelegationEntry), 'pending_withdraws' (array of PendingWithdrawEntry), 'rewards_pool' (Balance), 'starting_epoch' (uint64), 'sui_balance' (uint64), 'validator_address' (SuiAddress).
    *   ValidatorMetadata schema with required fields: 'name' (byte array), 'net_address' (byte array), 'network_pubkey_bytes' (byte array), 'next_epoch_commission_rate' (uint64), 'next_epoch_delegation' (uint64), 'next_epoch_gas_price' (uint64), 'next_epoch_stake' (uint64), 'proof_of_possession_bytes' (byte array), 'pubkey_bytes' (byte array), 'sui_address' (SuiAddress).
    *   Validator schema with required fields: 'commission_rate' (uint64), 'delegation_staking_pool' (StakingPool), 'gas_price' (uint64), 'metadata' (ValidatorMetadata), 'pending_stake' (uint64), 'pending_withdraw' (uint64), 'stake_amount' (uint64).
    *   ValidatorPair schema with required fields: 'from' (SuiAddress), 'to' (SuiAddress).
    *   ValidatorSet schema with required fields: 'active_validators' (array of Validator), 'delegation_stake' (uint64), 'next_epoch_validators' (array of ValidatorMetadata), 'pending_delegation_switches' (VecMap_for_ValidatorPair_and_Array_of_PendingWithdrawEntry), 'pending_removals' (uint64 array), 'pending_validators' (array of Validator), 'quorum_stake_threshold' (uint64), 'validator_stake' (uint64).
    *   SuiSystemState schema with required fields: 'epoch' (uint64), 'info' (UID), 'parameters' (SystemParameters), 'reference_gas_price' (uint64), 'storage_fund' (Balance), 'treasury_cap' (Supply), 'validator_report_records' (VecMap_for_SuiAddress_and_VecSet_for_SuiAddress), 'validators' (ValidatorSet).
    *   Collection wrapper schemas: VecMap_for_SuiAddress_and_VecSet_for_SuiAddress, VecMap_for_ValidatorPair_and_Array_of_PendingWithdrawEntry, VecSet_for_SuiAddress, Entry_for_SuiAddress_and_VecSet_for_SuiAddress, Entry_for_ValidatorPair_and_Array_of_PendingWithdrawEntry.

*   Update the `crates/sui-open-rpc/spec/openrpc.json` file to include the sui_getSuiSystemState method and all new schema types. Ensure schema type names match those defined by the generic VecMap/VecSet/Entry types instantiated with SuiAddress.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.