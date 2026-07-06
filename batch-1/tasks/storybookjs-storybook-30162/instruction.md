Extend the automated migration tool to update both the Vitest setup file and the project preview configuration file for accessibility testing. Ensure the tool checks for existing configurations and only applies necessary transformations. Update user prompts to reflect the automated and manual steps required.

*   Implement the `addonA11yAddonTest.check()` function:
    *   Return `null` if both the Vitest setup file contains `@storybook/addon-a11y` and the preview file contains `a11y-test`.
    *   Return an object with fields: `setupFile`, `previewFile`, `transformedSetupCode`, `transformedPreviewCode`, `skipPreviewTransformation`, and `skipVitestSetupTransformation`.
    *   Set `skipVitestSetupTransformation` to `true` and `transformedSetupCode` to `null` if the Vitest setup file already contains `@storybook/addon-a11y`.
    *   Set `skipPreviewTransformation` to `true` and `transformedPreviewCode` to `null` if the preview file already contains `a11y-test`.
    *   Handle file read errors by setting the corresponding transformed code field to `null`.

*   Implement the `addonA11yAddonTest.prompt()` function:
    *   Accept an object with fields: `setupFile`, `previewFile`, `transformedSetupCode`, `transformedPreviewCode`, `skipPreviewTransformation`, and `skipVitestSetupTransformation`.
    *   Generate a numbered-step message; include steps only when the corresponding skip flag is `false`.
    *   Include the introduction text: 'We have detected that you have @storybook/addon-a11y and @storybook/experimental-addon-test installed...'.
    *   Provide manual instructions when `transformedSetupCode` or `transformedPreviewCode` is `null`.
    *   Include a 'For more information' footer when any step requires manual action.

*   Implement the `addonA11yAddonTest.run()` function:
    *   Write `transformedPreviewCode` to `previewFile` using `utf8` encoding when both are non-null.
    *   Continue writing `transformedSetupCode` to `setupFile` when both are non-null.

*   Implement the `transformPreviewFile(source: string, filePath: string)` function:
    *   Export as an async function from `addon-a11y-addon-test.ts`.
    *   Return the source unchanged if the tags property already includes 'a11y-test' or '!a11y-test'.
    *   Insert a comment block followed by 'tags: [/*\'a11y-test\'*/]' when processing a typed preview config with no existing tags property.
    *   Append 'export const tags = ["a11y-test"];' when processing source with no default export.
    *   Add 'a11y-test' as a trailing block comment on the last element of an existing tags array if it does not include 'a11y-test'.
    *   Apply the same comment and commented-out tag logic for 'export default { ... }' objects without a typed variable.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.