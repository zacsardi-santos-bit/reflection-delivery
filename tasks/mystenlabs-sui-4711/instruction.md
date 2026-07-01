Implement comprehensive end-to-end operational support in the Sui TypeScript SDK to handle reading transaction data, reading owned objects, and executing transactions. Add a local transaction builder for client-side transaction byte construction, and provide a provider variant with caching capabilities.

*   Implement the `LocalTxnDataSerializer` class:
    *   Available as a named export from the SDK's root index.
    *   Constructor accepts a single `Provider` instance.
    *   Implements the `TxnDataSerializer` interface for local transaction byte construction.
    *   Supports operations: split-coin, merge-coin, Move call, transfer-object, and transfer-SUI.

*   Implement the `JsonRpcProviderWithCache` class:
    *   Available as a named export from the SDK's root index.
    *   Extends `JsonRpcProvider`, adding in-memory object caching.
    *   Constructor accepts a URL string.
    *   Usable as a drop-in replacement for `JsonRpcProvider`.

*   Update `RawSigner` class:
    *   Accepts an optional third constructor argument (`TxnDataSerializer`).
    *   Uses provided serializer for local transaction byte construction.
    *   Exposes methods: `splitCoin`, `mergeCoin`, `executeMoveCall`, `transferObject`, `transferSui`.
    *   Each method returns a `SuiTransactionResponse` with `getExecutionStatusType` returning 'success'.
    *   Exposes methods with request type: `splitCoinWithRequestType`, `mergeCoinWithRequestType`, `executeMoveCallWithRequestType`, `transferSuiWithRequestType`, `transferObjectWithRequestType`.
    *   Each method returns a `SuiExecuteTransactionResponse` with `getExecutionStatusType` returning 'success'.

*   Add `getCoinBalancesOwnedByAddress` to `JsonRpcProvider`:
    *   Returns a `Promise<GetObjectDataResponse[]>` of full coin object data.
    *   Filters objects owned by the address to coins, optionally by type argument.
    *   Declared abstract on `Provider` and implemented as a throwing stub on `VoidProvider`.

*   Update `getExecutionStatusType` and `getExecutionStatus` functions:
    *   Accept both `SuiTransactionResponse` and `SuiExecuteTransactionResponse`.
    *   Return execution status based on presence of 'effects' or 'EffectsCert' fields.
    *   Return `undefined` if neither structure is recognized.

*   Implement additional methods in `JsonRpcProvider`:
    *   `getTotalTransactionNumber`: Returns a `Promise<number>`.
    *   `getRecentTransactions`: Returns a `Promise<[number, string][]>`.
    *   `getTransactionWithEffects`: Returns a `Promise<SuiTransactionResponse>`.
    *   `getObjectsOwnedByAddress`: Returns a `Promise<SuiObjectInfo[]>`.
    *   `getGasObjectsOwnedByAddress`: Returns a `Promise<SuiObjectInfo[]>`.
    *   `getObject`: Returns a `Promise<GetObjectDataResponse>`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.