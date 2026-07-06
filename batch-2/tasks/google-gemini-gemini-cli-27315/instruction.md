Enhance the model fallback system to track and manage model replacements when the primary model fails. Implement a mechanism to store and query mappings from failed models to their replacements, ensure chain flattening for multi-step fallbacks, and manage runtime model overrides effectively.

*   Update the `activateFallbackMode` method in `Config` class:
    *   Accept an optional `failedModel` parameter.
    *   Store a mapping from `failedModel` to the fallback model.
    *   Register a runtime model override in `ModelConfigService`.
    *   Implement chain flattening: update all prior mappings pointing to `failedModel` to point to the new fallback model.
    *   Avoid resetting the model availability service if the fallback model is the same as the currently active model.

*   Implement the `getFallbackOverride` method in `Config` class:
    *   Return the fallback model for a given `model` string or `undefined` if no override exists.

*   Implement the `clearRuntimeOverrides` method in `ModelConfigService`:
    *   Remove all runtime model overrides.

*   Update the `refreshAuth` method in `Config` class:
    *   Clear all stored fallback overrides.
    *   Call `clearRuntimeOverrides` on `ModelConfigService`.

*   Update the `setSessionId` method in `Config` class:
    *   Clear all stored fallback overrides when the session ID changes.
    *   Call `clearRuntimeOverrides` on `ModelConfigService`.

*   Update the `setModel` method in `Config` class:
    *   Preserve existing fallback overrides when changing the preferred model.

*   Ensure `handleFallback` triggers `activateFallbackMode` with `undefined` as `failedModel` if the failed model is the same as the currently active model.

*   Modify `createAvailabilityServiceMock` in `packages/core/src/availability/testUtils.ts`:
    *   Provide a default return value of `{ available: true }` for the snapshot mock.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.