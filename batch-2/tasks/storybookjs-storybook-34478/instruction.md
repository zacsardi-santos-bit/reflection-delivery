Implement a coordinated status-clearing API in the Storybook addons manager. Add a method to signal all registered test providers to clear their status data, ensuring robust error handling and extensibility for third-party providers.

*   Add a `clearStatuses()` method to the addons API:
    *   Implement this method in `code/core/src/manager-api/modules/addons.ts`.
    *   Declare it on the `SubAPI` interface as `clearStatuses: () => void`.
    *   Ensure it iterates over all registered experimental test providers.
    *   Call the `clear()` function on each provider that has one.
    *   Skip providers without a `clear` property without throwing errors.
    *   Catch any errors from `clear()` calls and continue processing remaining providers without propagating errors to the caller.

*   Update the `Addon_TestProviderType` interface:
    *   Located in `code/core/src/types/modules/addons.ts`.
    *   Declare an optional `clear?: () => void` property for test providers to implement a status reset callback.

*   Implement the `clear` function for the built-in test provider:
    *   Modify the vitest addon test provider registration in `code/addons/vitest/src/manager.tsx`.
    *   Add a `clear` function that calls `componentTestStatusStore.unset()` and `a11yStatusStore.unset()` to reset accumulated test and accessibility statuses.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.