Implement a new core action to retrieve a transaction receipt by transaction hash, with optional chain targeting. Create corresponding query utilities and a React hook for integration with the library's reactive query system. Ensure all new features are accessible via the library's standard public exports.

*   Implement the `getTransactionReceipt` function:
    *   Location: `packages/core/src/actions/getTransactionReceipt.ts`
    *   Signature: `getTransactionReceipt(config: Config, parameters: { hash: Hash, chainId?: number }): Promise<TransactionReceipt>`
    *   Functionality: Fetches a transaction receipt using the transaction hash and optional chain ID.
    *   Export as a named export from:
        *   `packages/core/src/exports/actions.ts`
        *   `packages/core/src/exports/index.ts`
        *   `packages/react/src/exports/actions.ts`

*   Implement the `getTransactionReceiptQueryOptions` function:
    *   Location: `packages/core/src/query/getTransactionReceipt.ts`
    *   Signature: `getTransactionReceiptQueryOptions(config: Config, options?: { hash?: Hash, chainId?: number, scopeKey?: string }): { queryFn: Function, queryKey: readonly ["getTransactionReceipt", { hash?: Hash, chainId?: number }] }`
    *   Functionality: Returns query options compatible with TanStack Query for fetching a transaction receipt.
    *   Ensure `queryKey` includes `chainId` if provided.
    *   Export as a named export from:
        *   `packages/core/src/exports/query.ts`
        *   `packages/react/src/exports/query.ts`

*   Implement the `getTransactionReceiptQueryKey` function:
    *   Location: `packages/core/src/query/getTransactionReceipt.ts`
    *   Signature: `getTransactionReceiptQueryKey(options: { hash?: Hash, chainId?: number, scopeKey?: string }): readonly ["getTransactionReceipt", { hash?: Hash, chainId?: number }]`
    *   Functionality: Returns the query key used by `getTransactionReceiptQueryOptions`.
    *   Export as a named export from:
        *   `packages/core/src/exports/query.ts`
        *   `packages/react/src/exports/query.ts`

*   Implement the `useTransactionReceipt` hook:
    *   Location: `packages/react/src/hooks/useTransactionReceipt.ts`
    *   Signature: `useTransactionReceipt(parameters?: { hash?: Hash, chainId?: number, config?: Config, scopeKey?: string, query?: object }): UseQueryReturnType`
    *   Functionality: React hook that fetches a transaction receipt.
        *   Stays idle when no `hash` is provided, returning `isPending: true`.
        *   Automatically fetches when a `hash` is available, transitioning to `isSuccess: true`.
        *   Includes the active chain's `chainId` in `queryKey` even if not explicitly provided.
    *   Export as a named export from `packages/react/src/exports/index.ts`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.