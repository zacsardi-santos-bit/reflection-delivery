Implement a new operation in the liquid staking hub contract to finalize the receipt of tokens after an unbonding batch's waiting period has completed. Ensure the operation communicates with the staker contract and handles errors appropriately.

*   Update the lst contract:
    *   Add `ExecuteMsg::ReceiveBatch { batch_id: BatchId }` to `cosmwasm/lst/src/msg.rs`.
        *   Ensure this message is nonpayable.
    *   Implement the `ReceiveBatch` handler:
        *   Check if `batch_id` exists in `SubmittedBatches` storage.
            *   If not, return `ContractError::BatchNotFound { batch_id }`.
        *   Verify if the current block time (`env.block.time.seconds()`) is greater than the batch's `receive_time`.
            *   If not, return `ContractError::BatchNotReady { now: u64, ready_at: u64 }`.
        *   Emit a Wasm execute message to the staker contract using `StakerExecuteMsg::ReceiveUnstakedTokens { batch_id }` with no funds.
        *   On success, include an event named 'receive_batch' with attributes:
            *   'batch_id' as the numeric string of the batch id.
            *   'amount' as the string representation of `expected_native_unstaked`.

*   Update the lst-staker contract:
    *   Ensure `StakerExecuteMsg::ReceiveUnstakedTokens { batch_id: BatchId }` is only callable by the configured LST hub address.
        *   Return `ContractError::OnlyLstHub { sender: Addr }` if called by any other sender.
    *   Implement a `SetLstHubAddress` struct in `cosmwasm/lst-staker/src/event.rs`.
        *   Ensure it implements `Into<Event>` for the `set_lst_hub_address` event.

*   Ensure error handling:
    *   `ContractError::BatchNotFound` in `cosmwasm/lst/src/error.rs` for nonexistent batches.
    *   `ContractError::BatchNotReady` in `cosmwasm/lst/src/error.rs` for batches not ready to receive.
    *   `ContractError::OnlyLstHub` in `cosmwasm/lst-staker/src/error.rs` for unauthorized staker contract calls.

*   Utilize the `SubmittedBatches` storage in `cosmwasm/lst/src/state.rs`:
    *   Store batch data including `receive_time` and `expected_native_unstaked`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.