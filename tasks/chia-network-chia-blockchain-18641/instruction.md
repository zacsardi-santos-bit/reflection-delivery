Refactor the coin management commands in the Chia blockchain CLI to integrate with the shared class-based command framework. Ensure consistent behavior across commands by implementing wallet synchronization checks and graceful error handling. Update CLI flags for transaction files to use descriptive names.

*   Implement `NeedsCoinSelectionConfig` as a dataclass in `chia/cmds/cmd_classes.py`:
    *   Fields: `min_coin_amount`, `max_coin_amount`, `amounts_to_exclude`, `coins_to_exclude`.
    *   CLI options: `--min-coin-amount`, `--max-coin-amount`, `--exclude-amount`, `--exclude-coin`.
    *   Method: `load_coin_selection_config(amount: int) -> CoinSelectionConfig`.

*   Implement `NeedsTXConfig` as a dataclass in `chia/cmds/cmd_classes.py`:
    *   Fields: same as `NeedsCoinSelectionConfig` plus `reuse`.
    *   CLI option: `--new-address`.
    *   Method: `load_tx_config(amount: int, kwargs: dict, default: int) -> TXConfig`.

*   Implement `TransactionEndpoint` as a base class in `chia/cmds/cmd_classes.py`:
    *   Fields: `rpc_info`, `tx_config_loader`, `transaction_writer`, `fee`, `push`, `valid_at`, `expires_at`.
    *   CLI options: `--fee`, `--push/--no-push`, `--valid-at`, `--expires-at`.
    *   Method: `load_condition_valid_times() -> ConditionValidTimes`.
    *   Raise `TypeError` with 'transaction_endpoint_runner' if `run()` is not decorated.

*   Implement `transaction_endpoint_runner` as a decorator in `chia/cmds/cmd_classes.py`:
    *   Sets `_TRANSACTION_ENDPOINT_DECORATOR_APPLIED` on the wrapped function.
    *   Ensure `TransactionEndpoint` subclasses' `run()` methods are decorated.

*   Define `_TRANSACTION_ENDPOINT_DECORATOR_APPLIED` as a string constant in `chia/cmds/cmd_classes.py`.

*   Implement `TransactionEndpointWithTimelocks` as a subclass of `TransactionEndpoint`:
    *   Include timelock parameters.
    *   Match CLI parameters with `tx_out_cmd(enable_timelock_args=True)`.

*   Move `TransactionsIn` and `TransactionsOut` from `chia.cmds.signer` to `chia.cmds.cmd_classes`:
    *   `TransactionsIn`: Field `transaction_file_in`, CLI arg `--transaction-file-in`.
    *   `TransactionsOut`: Field `transaction_file_out`, CLI arg `--transaction-file-out`.

*   Implement `ListCMD` in `chia/cmds/coins.py`:
    *   Fields: `rpc_info`, `coin_selection_config`, `id`, `show_unconfirmed`, `paginate`.
    *   CLI args: `--id`, `--show-unconfirmed`, `--paginate/--no-paginate`.
    *   Output: Coin counts and details, handle wallet not found and not synced cases.
    *   Support pagination: 5 coins per page, 'c' to continue, 'q' to quit.

*   Implement `CombineCMD` in `chia/cmds/coins.py` extending `TransactionEndpoint`:
    *   Fields: `id`, `target_amount`, `number_of_coins`, `input_coins`, `largest_first`.
    *   CLI args: `--id`, `--target-amount`, `--number-of-coins`, `--input-coin`, `--largest-first`.
    *   Decorate `run()` with `@transaction_endpoint_runner`.
    *   Require user confirmation before combining.

*   Implement `SplitCMD` in `chia/cmds/coins.py` extending `TransactionEndpoint`:
    *   Fields: `id`, `number_of_coins`, `amount_per_coin`, `target_coin_id`.
    *   CLI args: `--id`, `--number-of-coins`, `--amount-per-coin`, `--target-coin-id`.
    *   Decorate `run()` with `@transaction_endpoint_runner`.
    *   Handle no transaction output when `number_of_coins=0`.

*   Define `STANDARD_TX_ENDPOINT_ARGS` as a dict constant in `chia/_tests/environments/wallet.py`.

*   Implement `cmd_tx_endpoint_args` method in `WalletTestFramework`:
    *   Returns constructor kwargs for `TransactionEndpoint` commands.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.