Update the block styling system to correctly handle CSS class names and custom properties. Implement a utility function to extract and flatten custom property entries from style objects. Ensure the Sitemap component renders correctly in multilingual configurations.

*   Modify the `buildStyleClassNamesFromData` function:
    *   Skip keys starting with '--' when generating class names.
    *   Example: For `{ color: 'red', '--background-color': '#FFF' }`, return `['has--color--red']`.

*   Implement and export `buildStyleObjectFromData` from `src/helpers/Blocks/Blocks.js`:
    *   Return an empty object if no keys start with '--'.
    *   Extract and return only CSS custom property entries for keys starting with '--'.
    *   Recursively process nested objects, flattening custom property keys using '--' as a separator.
    *   Example: `{ color: 'red', '--background-color': '#FFF' }` returns `{ '--background-color': '#FFF' }`.
    *   Example: A key '--foo' nested under 'nested' becomes '--nested--foo'.

*   Update the Sitemap component:
    *   Ensure it renders correctly when `config.settings.isMultilingual` is true.
    *   Handle `config.settings.supportedLanguages` as an array of language codes.
    *   Ensure proper rendering with a `location` prop having a language-rooted path (e.g., '/en/').

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.