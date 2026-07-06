Implement a comprehensive test suite for the cross-chain token transfer application contract, covering core packet handling and token minting behaviors. Ensure the contract enforces access control, reentrancy protection, address validation, and correct token minting logic.

*   Implement the `reverse_channel_path` function:
    *   Change the return type to `Result<U256, ContractError>`.
    *   Ensure it returns `Ok(reversed_path)` on success.

*   Implement access control for internal execute message variants:
    *   Return `ContractError::OnlySelf` when `InternalBatch`, `InternalExecutePacket`, or `InternalWriteAck` are called by any address other than the contract itself.

*   Implement reentrancy protection in `OnRecvPacket`:
    *   Validate caller and relayer addresses as valid bech32 addresses.
    *   Return `ContractError::Std` with message 'Error decoding bech32' for invalid addresses.
    *   Save the packet to `EXECUTING_PACKET` storage item at the start.
    *   Return `ContractError::AlreadyExecuting` if `EXECUTING_PACKET` is already set.

*   Implement end-to-end token minting:
    *   Deploy a new CW20 token contract and mint tokens to the receiver on valid `FungibleAssetOrder`.
    *   Write a success acknowledgment with `TAG_ACK_SUCCESS` and `FungibleAssetOrderAck` with `FILL_TYPE_PROTOCOL`.

*   Implement fee distribution logic:
    *   Distribute `base_amount - quote_amount` as a fee to the relayer when `base_amount > quote_amount`.

*   Implement order handling:
    *   Return `ContractError::OnlyMaker` when `base_amount < quote_amount` or when the quote token is a native token.

*   Implement token origin tracking:
    *   Store the token's origin in `TOKEN_ORIGIN` after minting, using the result of `update_channel_path(packet_path, destination_channel_id)`.

*   Implement acknowledgment logic:
    *   Write a failure acknowledgment with `TAG_ACK_FAILURE` for invalid packets.

*   Implement proxy deployment:
    *   Support deployment via a proxy/migrate pattern using `frissitheto::UpgradeMsg::Init`.
    *   Ensure `QueryMsg::GetMinter` returns the address of the instantiated minter contract.

*   Implement the `PredictWrappedToken` query:
    *   Return a `PredictWrappedTokenResponse` with a deterministic `wrapped_token` address.

*   Ensure ABI encoding/decoding for:
    *   `Ack`, `FungibleAssetOrder`, `FungibleAssetOrderAck`, and `ZkgmPacket` structs.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.