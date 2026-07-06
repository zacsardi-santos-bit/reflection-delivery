Refactor the plotnft command-line interface in the Chia blockchain codebase to enhance testability and error handling. Implement each command as an instantiable class with an async run method, and ensure proper exceptions are raised for error conditions.

*   Implement eight command classes in `chia/cmds/plotnft.py`:
    *   CreatePlotNFTCMD
    *   ShowPlotNFTCMD
    *   JoinPlotNFTCMD
    *   LeavePlotNFTCMD
    *   ClaimPlotNFTCMD
    *   InspectPlotNFTCMD
    *   ChangePayoutInstructionsPlotNFTCMD
    *   GetLoginLinkCMD
    *   Each class must have an async `run()` method.

*   CreatePlotNFTCMD:
    *   Raise `CliRpcConnectionError` with "is not allowed with 'local' state" when `state='local'` and `pool_url` is provided.
    *   Raise `CliRpcConnectionError` with "is required with 'pool' state" when `state='pool'` and `pool_url` is None or empty.

*   ShowPlotNFTCMD:
    *   Raise `CliRpcConnectionError` with "is not a pool wallet" when the specified `id` does not correspond to a pool wallet.
    *   Output must include "Current state:" and "Wallet ID:" lines for valid pool wallets.

*   LeavePlotNFTCMD:
    *   Raise `CliRpcConnectionError` with "No pool wallet found" when `id=None` and no pool wallets exist.
    *   Raise `CliRpcConnectionError` with "is not a pool wallet" when the specified `id` is not a pool wallet.

*   JoinPlotNFTCMD:
    *   Raise `CliRpcConnectionError` for:
        *   "No pool wallet found" when `id=None` and no pool wallets exist.
        *   "is not a pool wallet" when `id` is not a pool wallet.
        *   "must be HTTPS on mainnet" for HTTP URLs on mainnet.
        *   "Error connecting to pool" when the pool URL is unreachable.
        *   "Relative lock height too high for this pool" when lock height exceeds the limit.
        *   "Incorrect version" for mismatched protocol versions.
        *   "More than one pool wallet" when `id=None` and multiple pool wallets exist.

*   ClaimPlotNFTCMD:
    *   Raise `CliRpcConnectionError` with "No pool wallet found" when `id=None` and no pool wallets exist.
    *   Raise `CliRpcConnectionError` with "is not a pool wallet" when `id` does not correspond to a pool wallet.

*   InspectPlotNFTCMD:
    *   Print a JSON object with a "pool_wallet_info" key containing a "current" sub-object with "owner_pubkey" and "state" fields.
    *   Raise `CliRpcConnectionError` for:
        *   "No pool wallet found" when `id=None` and no pool wallets exist.
        *   "is not a pool wallet" for a non-pool wallet `id`.
        *   "More than one pool wallet" when `id=None` and multiple pool wallets exist.

*   ChangePayoutInstructionsPlotNFTCMD:
    *   Print "{launcher_id.hex()} Not found." when `launcher_id` is not in the pool config.
    *   Update `payout_instructions` to the raw puzzle hash hex and print "Payout Instructions for launcher id: {launcher_id.hex()} successfully updated".

*   GetLoginLinkCMD:
    *   Raise `CliRpcConnectionError` with "Was not able to get login link" when the login link cannot be obtained.

*   Update `create` function in `chia/cmds/plotnft_funcs.py`:
    *   Accept `wallet_info: WalletClientInfo` as the first parameter.
    *   Raise `CliRpcConnectionError` with "Pool URLs must be HTTPS on mainnet" for HTTP URLs on mainnet.
    *   Raise `ValueError` with "Plot NFT must be created in SELF_POOLING or FARMING_TO_POOL state" for unsupported state values.
    *   Raise `CliRpcConnectionError` with "Error creating plot NFT: {error_message}" on wallet creation failure.

*   Ensure pool wallet creation uses `get_puzzle_hash(new: bool)` respecting the `reuse_puzhash` configuration.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.