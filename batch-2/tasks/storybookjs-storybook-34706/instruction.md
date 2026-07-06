Implement a more robust AI setup checklist that accurately reflects the setup status for individual projects within a monorepo and accommodates users with telemetry disabled. Ensure the checklist only marks the AI setup as complete when the setup command has run for the specific project and at least one AI-generated story exists.

*   Update the `hasAiInitOptIn` function:
    *   Read from the cache key 'ai-init-opt-in'.
    *   Return `true` if the cached entry contains a `configDir` field matching the absolute path of the provided `configDir` argument.
    *   Return `false` if there is no cached entry, the entry lacks a `configDir` field, or the cached `configDir` does not match.

*   Update the `hasAiSetupRun` function:
    *   Read from the cache key 'ai-setup-ran'.
    *   Return `true` if the cached entry contains a `configDir` field matching the absolute path of the provided `configDir` argument.
    *   Return `false` if there is no cached entry, the entry lacks a `configDir` field, or the cached `configDir` does not match.
    *   Resolve relative `configDir` paths against the current working directory.

*   Both `hasAiInitOptIn` and `hasAiSetupRun` must:
    *   Import the cache object from 'storybook/internal/common'.
    *   Handle cached entry shape: `{ timestamp: number, configDir: string }`.

*   Modify the `initializeChecklist` function:
    *   Accept two new optional parameters: `storyIndexGeneratorGetter` and `configDir`.
    *   Set the `aiSetup` checklist item status to 'done' only if `hasAiSetupRun` returns `true` for the given `configDir` and the story index contains at least one story with the 'ai-generated' tag.
    *   Include an `aiOptIn` field in the store state, set to `true` if `hasAiInitOptIn` returns `true` for the given `configDir`.
    *   Ensure initialization does not block the initial 'loaded' state while AI flag checks are pending.
    *   Complete initialization even if reading from the AI cache throws an error.

*   Ensure ghost stories and analytics channel events:
    *   Are not emitted if `ai-setup` ran but no AI-generated stories exist.
    *   Are emitted after a 4-minute idle delay if a completed agent run is detected at startup or mid-session.

*   Apply the 'done' override even if the persisted `aiSetup` status was 'skipped', provided `ai-setup` ran and AI-generated stories are present.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.