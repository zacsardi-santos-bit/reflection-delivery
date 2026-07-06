Implement an automated codemod for the React Native Storybook generator to modify a project's Metro bundler configuration during initialization. Ensure the codemod detects the module format, injects the Storybook integration, and handles various edge cases like existing imports, multiple config files, and Expo projects.

*   Implement `runMetroCodemodOrFallback` to:
    *   Locate a `metro.config.js` (or `.ts/.mjs`) file in the current directory.
    *   Transform the file by injecting the Storybook wrapper import and wrapping the exported config.
    *   Return `{ status: 'updated' }` if the transformation is applied.
    *   Return `{ status: 'skipped-existing-storybook-import' }` if a Storybook import is already present, ensuring idempotency.
    *   Prompt the user to select a config file if multiple are found and `yes` is false, returning the selected `filePath`.
    *   Return `{ status: 'skipped-missing-file' }` if no config file is found and `yes` is true.
    *   Use Expo CLI to generate a Metro config if Expo is detected, logging a warning if it fails.
    *   Prompt for a custom config path if no file is found and `yes` is false.
    *   Prepend a fallback comment and return `{ status: 'fallback-commented' }` if the config shape is unrecognized.

*   Implement `transformMetroConfigSource` to:
    *   Return `{ action: 'updated', code: <transformed> }` for supported config shapes.
    *   Return `{ action: 'already-configured' }` if the export is already wrapped with the Storybook helper.
    *   Use ESM import syntax for `.ts` and `.mjs` files, and `.js` files with `export default`.
    *   Use CJS require syntax for `.js` and `.ts` files with `module.exports`.
    *   Reuse existing aliased imports or requires for `withStorybook`.
    *   Preserve TypeScript type parameters and return type annotations on exported functions.
    *   Keep leading pragma comments or directives as the first content in the file.

*   Implement `containsStorybookImport` to:
    *   Return true if the source contains any import or require of a Storybook package.
    *   Use a heuristic for unparseable sources to detect package specifiers starting with '@storybook/' or 'storybook/'.

*   Implement `prependMetroFallbackComment` to:
    *   Prepend a fallback comment block containing `METRO_FALLBACK_COMMENT_MARKER`.
    *   Ensure idempotency by returning the source unchanged if the marker is already present.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.