Implement support for glob-importing JavaScript files from directories outside the configured project root in a Vite project. Ensure the imports work correctly when using an absolute base path option with eager loading, and update the playground to demonstrate this functionality.

Requirements:

*   Support glob import patterns that reference files outside the project root directory.
    *   Ensure compatibility when combined with an absolute base path option and eager loading.
*   When using a glob pattern like '../external/*.js' with an absolute base path:
    *   The resulting module map keys must be relative paths from the importing file to each matched file (e.g., '../external/x.js', '../external/y.js').
*   Create two external JavaScript fixture files:
    *   `playground/glob-import/external/x.js` — export a named constant `msg` with the value `'hello from x'`.
    *   `playground/glob-import/external/y.js` — export a named constant `msg` with the value `'hello from y'`.
*   Update the playground HTML page:
    *   File: `playground/glob-import/root/index.html`
    *   Add a `<pre>` element with class `absolute-base-outside-root`.
    *   Include a module script that:
        *   Uses `import.meta.glob('../external/*.js', { eager: true, base: '/' })`.
        *   Maps over the entries, extracting the `msg` property from each module's exports.
        *   Serializes the result as a JSON object into the `.absolute-base-outside-root` element.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.