Implement a feature to improve the model selection experience for users without Pro-tier access by filtering out Pro models and adjusting model selection behavior. Add a new lightweight flash preview model visible only to free-tier users.

*   Export a new constant `PREVIEW_GEMINI_3_1_FLASH_LITE_MODEL` from `packages/core/src/config/models.ts` with the value `'gemini-3.1-flash-lite-preview'`.
    *   Re-export this constant from the core package's public index.
*   Ensure `getDisplayString` returns the model identifier string unchanged when called with `PREVIEW_GEMINI_3_1_FLASH_LITE_MODEL`.
*   Update `resolveModel` to return `DEFAULT_GEMINI_FLASH_LITE_MODEL` when called with `PREVIEW_GEMINI_3_1_FLASH_LITE_MODEL` and preview access is false.
*   Modify `isActiveModel` to return true for `PREVIEW_GEMINI_3_1_FLASH_LITE_MODEL` regardless of the `useGemini3_1` flag.
*   Add `PRO_MODEL_NO_ACCESS` to `ExperimentFlags` in `packages/core/src/code_assist/experiments/flagNames.ts` with the value `45768879`.
*   Extend the `Config` class in `packages/core/src/config/config.ts`:
    *   Implement `getProModelNoAccess(): Promise<boolean>` to resolve true if the `PRO_MODEL_NO_ACCESS` flag is enabled.
    *   Implement `getProModelNoAccessSync(): boolean` to return true if the `PRO_MODEL_NO_ACCESS` flag is enabled for `LOGIN_WITH_GOOGLE` auth types.
*   Update `Config.refreshAuth` to switch the model to `PREVIEW_GEMINI_FLASH_MODEL` if `getProModelNoAccess()` returns true and the current model is auto-selected.
*   Modify the `ModelDialog` component in `packages/cli/src/ui/components/ModelDialog.tsx`:
    *   Initialize in 'manual' view if `getProModelNoAccessSync()` returns true.
    *   Asynchronously call `getProModelNoAccess()` after mounting to set the view to 'manual' if true.
    *   Exclude Pro models from the model list when the user lacks Pro access using `isProModel()`.
    *   Include `PREVIEW_GEMINI_3_1_FLASH_LITE_MODEL` for free-tier users with preview access, ordering it after `PREVIEW_GEMINI_FLASH_MODEL`.
    *   Ensure pressing Escape closes the dialog if the user lacks Pro access or remains in 'manual' view otherwise.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.