Implement robust handling for the model resolution and classification logic to ensure predictable behavior when the model flag is specified multiple times or when non-string values are encountered. Update the relevant functions to handle these scenarios gracefully.

*   Update the `resolveModel` function in `packages/core/src/config/models.ts`:
    *   If `requestedModel` is an array, return the string form of the last element.
    *   If `requestedModel` is a non-string, non-array value, coerce it to a string.
    *   Ensure normal string behavior remains unchanged.

*   Update the `isCustomModel` function in `packages/core/src/config/models.ts`:
    *   Ensure it does not throw an error when given an array input.
    *   Evaluate the last element of the array to determine if it is a custom model.

*   Update the `loadCliConfig` function in `packages/cli/src/config/config.ts`:
    *   When `argv.model` is an array, ensure the resulting config's `getModel()` method returns the last element.
    *   When `argv.model` is a non-string, non-array value, ensure the resulting config's `getModel()` method returns the string coercion of that value.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.