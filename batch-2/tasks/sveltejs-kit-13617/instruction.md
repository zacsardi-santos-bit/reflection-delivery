Refactor the image tag transformation from a Svelte preprocessor to a Vite plugin transform. Ensure that CSS type selectors targeting the custom image element by its tag name are correctly rewritten to apply to the rendered element. Implement the necessary functions in a new file to facilitate this transformation.

*   Create a new file at `packages/enhanced-img/src/vite-plugin.js`.
*   Implement and export the `image_plugin` function:
    *   Accept a Vite plugin object with at least a `name` string and a `load` function.
    *   Return a Vite plugin object with a `transform` method.
    *   The `transform` method must:
        *   Be callable with a custom `this` context providing a `resolve(url)` function.
        *   Accept parameters `(content: string, filename: string)`.
        *   Return an object with a `code` property (string) and a `sourcemap` when transformations are applied.
        *   Return `null` or `undefined` if no transformation is needed.
*   Ensure the `transform` output matches the pre-existing `Output.svelte` snapshot when processing the `Input.svelte` test fixture.
*   Implement and export the `parse_object` function:
    *   Accept a minimized (compact, single-line) JavaScript-like object string with unquoted keys.
    *   Accept a non-minimized (formatted, multi-line) JavaScript-like object string with unquoted keys and whitespace/newline separators.
    *   Return a parsed JavaScript object for both input formats.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.