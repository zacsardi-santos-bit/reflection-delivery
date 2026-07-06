Implement support for the "watch asset" capability in a web3 frontend library, allowing decentralized applications to request that a user's wallet starts tracking a specific token. Create a core action, a mutation options factory, and a React hook to facilitate this functionality, ensuring they are accessible from the main package entry points.

*   Create a core action in `packages/core/src/actions/watchAsset.ts`:
    *   Accept a `config` object and a `parameters` object with fields: `type` (e.g., 'ERC20') and `options` containing `address` (string), `symbol` (string), and `decimals` (number).
    *   Return a `Promise` that resolves to a boolean indicating success.
    *   Export from `packages/core/src/exports/actions.ts` and `packages/core/src/exports/index.ts`.
    *   Re-export from `packages/react/src/exports/actions.ts` and `packages/vue/src/exports/actions.ts`.

*   Create a mutation options factory in `packages/core/src/query/watchAsset.ts`:
    *   Implement `watchAssetMutationOptions(config: Config)` to return an object with `mutationFn` (a function wrapping the core action) and `mutationKey` set to `["watchAsset"]`.
    *   Export from `packages/core/src/exports/query.ts`.
    *   Re-export from `packages/react/src/exports/query.ts` and `packages/vue/src/exports/query.ts`.

*   Develop a React hook in `packages/react/src/hooks/useWatchAsset.ts`:
    *   Return an object with `watchAsset` and `watchAssetAsync` methods, and state fields including `isSuccess` and `data`.
    *   Ensure `isSuccess` becomes true and `data` equals true after invoking `watchAsset` with valid parameters.
    *   Export from `packages/react/src/exports/index.ts`.

*   Ensure all new functionalities are accessible from the main package entry points:
    *   Export the core action and mutation options from the Vue package's actions and query exports.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.