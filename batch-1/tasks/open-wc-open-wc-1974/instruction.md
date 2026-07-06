Update the code generation logic for the Storybook markdown docs addon to align with the modern Storybook API. Ensure that story metadata is assigned directly to exported functions and update import paths to be project-type-aware.

*   Implement the `createStoriesCode` function in `packages/storybook-addon-markdown-docs/src/createStoriesCode.js`:
    *   Attach story parameters directly to the exported function using the format `StoryKey.parameters = StoryKey.parameters || {};`.
    *   Assign `StoryKey.parameters.mdxSource = ...;` directly on the export.
    *   When a story's name differs from its key, emit `StoryKey.storyName = "Story Name";` directly on the export.
    *   Do not emit any lines that create or reference an intermediate `.story` object.

*   Implement the `createDocsPage` function in `packages/storybook-addon-markdown-docs/src/mdToJsx.js`:
    *   Accept a second parameter, `projectType` (a string), and use it to generate the import path: `import { React, mdx } from '@web/storybook-prebuilt/${projectType}.js';`.
    *   Generate the `AddContext` import as: `import { AddContext } from '@web/storybook-prebuilt/addon-docs/blocks.js';`.
    *   Emit `__page.parameters = { docsOnly: true };` as a flat assignment when there are no stories.

*   Update the `mdjsToCsf` function in `packages/storybook-addon-markdown-docs/src/mdjsToCsf.js`:
    *   Use the function signature `mdjsToCsf(markdown, filePath, projectType, options)` with `projectType` as the third parameter.
    *   Ensure the `projectType` is passed through to the docs page generation step.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.