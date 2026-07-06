Implement a configurable network request timeout using a remote experiment flag, and ensure clear error messaging when the language model client is accessed prematurely. Update the system to apply the timeout to all outgoing HTTP requests, including those through a proxy, and improve error diagnostics for initialization.

*   Add a DEFAULT_REQUEST_TIMEOUT entry in `ExperimentFlags` with the value 45773134 in `packages/core/src/code_assist/experiments/flagNames.ts`.
*   Update the `Config` class in `packages/core/src/config/config.ts`:
    *   Implement `getRequestTimeoutMs(): number | undefined`:
        *   Return `undefined` if `DEFAULT_REQUEST_TIMEOUT` is not set.
        *   Convert `intValue` to milliseconds if it is a valid positive integer string.
        *   Return `undefined` for non-parseable or negative `intValue`.
    *   Ensure `getBaseLlmClient()` throws an error with the message 'BaseLlmClient not initialized. Ensure experiments have been fetched and configuration is ready.' if experiments are not set.
*   Modify `packages/core/src/utils/fetch.ts`:
    *   Add `updateGlobalFetchTimeouts(timeoutMs: number): void` to update the module-level timeout.
    *   Ensure `setGlobalProxy(proxy: string): void` uses the updated timeout for `headersTimeout` and `bodyTimeout` when constructing a `ProxyAgent`.
*   Ensure the `Config` interface includes `getRequestTimeoutMs(): number | undefined`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.