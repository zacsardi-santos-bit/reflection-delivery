Implement tier-aware model handling in the CLI to ensure non-premium users are not shown or assigned premium models they cannot access. Update the model selection dialog to reflect the user's access level and introduce a new model variant for free-tier users.

*   Define a new model constant:
    *   `PREVIEW_GEMINI_3_1_FLASH_LITE_MODEL` with value `'gemini-3.1-flash-lite-preview'`.
    *   Export from `packages/core/src/config/models.ts` and re-export via the core package's public interface.

*   Update functions in `models.ts`:
    *   Modify `getDisplayString(model: string): string` to return `'gemini-3.1-flash-lite-preview'` for `PREVIEW_GEMINI_3_1_FLASH_LITE_MODEL`.
    *   Ensure `isActiveModel(model: string, useGemini3_1?: boolean): boolean` returns `true` for `PREVIEW_GEMINI_3_1_FLASH_LITE_MODEL`.

*   Introduce an experiment flag:
    *   `ExperimentFlags.PRO_MODEL_NO_ACCESS` to indicate no access to Pro models.
    *   When `true` and the model is `PREVIEW_GEMINI_MODEL_AUTO`, switch to `PREVIEW_GEMINI_FLASH_MODEL` in `Config.refreshAuth()`.

*   Update the `Config` class in `config.ts`:
    *   Add `getProModelNoAccess(): Promise<boolean>` to check Pro model access.
    *   Add `getProModelNoAccessSync(): boolean` for synchronous access check.
    *   Add `getUserTier(): UserTierId | undefined` to determine user tier.

*   Define a `UserTierId` enum:
    *   Include `STANDARD` and `FREE` variants.
    *   Export from the core package's public interface.

*   Modify the `ModelDialog` component in `ModelDialog.tsx`:
    *   Use `config.getProModelNoAccessSync()` to render the manual selection view initially if no Pro access.
    *   Exclude Pro models and "Auto" option for users without Pro access.
    *   Display models in this order for no-pro-access users: Flash Preview → Flash Lite Preview → Flash → Flash Lite.
    *   Close the dialog on Escape when in initial manual mode.
    *   Show `PREVIEW_GEMINI_3_1_FLASH_LITE_MODEL` only for `UserTierId.FREE` users with preview access.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.