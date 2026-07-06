Implement updates to the model routing and stats display logic in the CLI tool to address issues with model selection and display when a newer GA flash model is available. Ensure the correct model identifiers are used based on authentication methods and that user-facing names are displayed in the stats.

*   Add a new constant:
    *   Define `DEFAULT_GEMINI_3_5_FLASH_MODEL` in `packages/core/src/config/models.ts` with the value `'gemini-3.5-flash'`.
    *   Include this constant in the `VALID_GEMINI_MODELS` set.

*   Update display functions:
    *   Modify `getDisplayString(model: string): string` to map `'gemini-3-flash'` to `DEFAULT_GEMINI_3_5_FLASH_MODEL`.
    *   Ensure `ModelStatsDisplay` and `StatsDisplay` use `getDisplayString` to render user-facing names instead of raw identifiers.

*   Enhance model description and resolution:
    *   Update `getAutoModelDescription` to accept a third boolean parameter `useGemini3_5Flash`. If true along with other conditions, include `DEFAULT_GEMINI_3_5_FLASH_MODEL` in the description.
    *   Modify `resolveModel` to accept a sixth boolean parameter `useGemini3_5Flash`. Adjust model resolution logic based on this parameter and dynamic configuration.
    *   Update `resolveClassifierModel` to accept a seventh boolean parameter `useGemini3_5Flash`. Adjust return values based on static or dynamic configuration.

*   Adjust access and routing logic:
    *   Update `hasGemini35FlashGAAccess` in `packages/core/src/config/config.ts` to set model identifiers based on authentication type and GA experiment flag.
    *   Ensure all routing strategies call `config.hasGemini35FlashGAAccess()` and use the result to determine the `useGemini3_5Flash` parameter for model resolution functions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.