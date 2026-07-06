Implement the necessary changes to correct the flash model routing logic and statistics display issues. Ensure that user-selected preview models are respected, authentication-based model assignments are correct, and display names in statistics are accurate.

*   Add a new exported constant:
    *   Name: `DEFAULT_GEMINI_3_5_FLASH_MODEL`
    *   Location: `packages/core/src/config/models.ts`
    *   Value: `'gemini-3.5-flash'`

*   Update the `getDisplayString` function:
    *   Location: `packages/core/src/config/models.ts`
    *   Map the model identifier `'gemini-3-flash'` to `DEFAULT_GEMINI_3_5_FLASH_MODEL`.

*   Modify the `getAutoModelDescription` function:
    *   Signature: `getAutoModelDescription(hasAccessToPreview: boolean, useGemini3_1?: boolean, useGemini3_5Flash?: boolean) -> string`
    *   Include `useGemini3_5Flash` as a parameter.
    *   Ensure the description contains `'gemini-3.1-pro-preview'` and `DEFAULT_GEMINI_3_5_FLASH_MODEL` when all input conditions are true.

*   Adjust the `resolveModel` function:
    *   Location: `packages/core/src/config/models.ts`
    *   In static mode with `useGemini3_5Flash=true`:
        *   Resolve flash alias and `DEFAULT_GEMINI_FLASH_MODEL` to `DEFAULT_GEMINI_FLASH_MODEL`.
        *   Keep `PREVIEW_GEMINI_FLASH_MODEL` as `'gemini-3-flash-preview'` if preview access is granted.
        *   Resolve `GEMINI_MODEL_ALIAS_AUTO` to `PREVIEW_GEMINI_MODEL` with preview access.
    *   In dynamic mode with `useGemini3_5Flash=true`:
        *   Resolve flash alias to `'gemini-3.5-flash'`.
        *   Resolve `PREVIEW_GEMINI_FLASH_MODEL` based on access.

*   Update the `resolveClassifierModel` function:
    *   Location: `packages/core/src/config/models.ts`
    *   In static mode with `useGemini3_5Flash=true`, resolve to `DEFAULT_GEMINI_FLASH_MODEL`.
    *   In dynamic mode, resolve to `'gemini-3.5-flash'`.

*   Export `PREVIEW_GEMINI_MODEL_AUTO` from `models.ts`.

*   Modify the `hasGemini35FlashGAAccess` method:
    *   Location: `packages/core/src/config/config.ts`
    *   Differentiate based on `authType`:
        *   For `AuthType.USE_GEMINI`, set `DEFAULT_GEMINI_FLASH_MODEL` to `'gemini-3.5-flash'` and `PREVIEW_GEMINI_FLASH_MODEL` to `'gemini-3-flash-preview'`.
        *   For other `authType` values, set both to `'gemini-3-flash'`.
    *   Return true if `GEMINI_3_5_FLASH_GA_LAUNCHED` flag is set.

*   Ensure all routing strategy modules route to `DEFAULT_GEMINI_FLASH_MODEL` when `config.hasGemini35FlashGAAccess()` returns true.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.