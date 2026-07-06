## Description

The liquid staking hub contract is missing the ability to finalize the receipt of tokens after an unbonding batch's waiting period has completed. When users unbond their liquid staking tokens, the system groups unbond requests into batches and submits them. However, once the unbonding period passes and the native tokens are returned, there is currently no mechanism for the hub to notify the underlying staker layer that the tokens have been received for a specific batch.

## Expected Behavior

- A new operation should be available on the hub contract to signal that a submitted unbonding batch's tokens have been received.
- When this operation is triggered for a batch whose unbonding period has elapsed, the hub should forward a notification to the staker contract with the corresponding batch identifier.
- The response should include an event recording both the batch identifier and the expected amount of tokens.
- If the operation is triggered for a batch that has not yet been submitted to the chain, it should be rejected with an appropriate "batch not found" error.
- If the operation is triggered before the batch's unbonding period has completed, it should be rejected with an appropriate "batch not ready" error that includes the current time and the time when the batch will be ready.
- This new operation must not accept any attached funds.
- On the staker side, the operation that handles incoming unstaked token receipts must be restricted to calls originating only from the trusted hub contract address. Unauthorized callers should be rejected.

## Why This Matters

Without this operation, the unbonding lifecycle is incomplete — tokens may be returned on-chain but the system has no way to acknowledge receipt and complete the process. This also closes a security gap where any address could previously invoke the receipt handler on the staker contract.
