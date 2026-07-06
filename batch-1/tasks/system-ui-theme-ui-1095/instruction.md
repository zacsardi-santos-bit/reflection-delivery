Update the `toCustomProperties` function to handle cases where the theme data is undefined. Ensure it returns an empty object instead of throwing an error or producing unexpected behavior.

*   Modify the `toCustomProperties` function located in `packages/color-modes/src/custom-properties.ts` (or `custom-properties.js`).
    *   Ensure the function signature is `toCustomProperties(obj: Record<string, any> | undefined, parent?: string, themeKey?: string) -> Record<string, any>`.
    *   Implement logic to accept `undefined` as the `obj` parameter without throwing an error.
    *   When `toCustomProperties` is called with `undefined` as the first argument, return an empty plain object `{}`.
    *   Maintain existing functionality for valid theme object inputs.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.