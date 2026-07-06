Implement a function to automate the migration of Storybook story files to a new factory-based format. This function should handle various existing story patterns in both JavaScript and TypeScript, converting them consistently to the new format.

*   Implement the `storyToCsfFactory` function with the following signature:
    *   `storyToCsfFactory(info: { source: string; path: string; [key: string]: any }) -> Promise<string>`
    *   Export this function as a named export from `code/lib/cli-storybook/src/automigrate/fixes/csf-factories.ts`.
*   Transformations:
    *   Convert `const`-declared meta variables followed by `export default meta` to use the config factory's meta method.
    *   Transform inline default-exported object literals to a `const meta` variable using the config factory's meta method.
    *   Rename any meta variable not named "meta" to "meta".
    *   Wrap named story export objects in a call to the meta's story method.
    *   Convert CSF1-style story exports with arrow functions to use meta.story calls with a `render` property.
*   Import Handling:
    *   Add the necessary import from the storybook preview config, merging it into any existing import from that path.
    *   If a root-level constant named "config" exists, alias the imported config as `storybookConfig` and use this alias in the factory calls.
*   TypeScript Handling:
    *   Strip TypeScript `satisfies` and `as` type annotations from meta and story declarations.
    *   Remove type-only imports from Storybook framework packages.
    *   Ensure identical output regardless of TypeScript annotation syntax variants used.
*   Ensure the output is suitable for formatting with the project's file content formatter.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.