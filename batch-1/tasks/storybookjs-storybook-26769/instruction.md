Implement a server-side feature in Storybook's core server to automatically generate story files for existing components. Ensure the system can handle both TypeScript and JavaScript components, and provide utilities for string conversion and path normalization.

*   Implement the `posix` function in `code/lib/core-server/src/utils/posix.ts`:
    *   Accept a path string and a separator string.
    *   Return the path with all occurrences of the separator replaced by forward slashes.

*   Implement the `getComponentVariableName` function in `code/lib/core-server/src/utils/get-component-variable-name.ts`:
    *   Convert a given string to a valid PascalCase variable name.
    *   Strip leading non-alphabetic characters, split on hyphens and spaces, capitalize each word, and remove most special characters (preserve '$').

*   Implement the `getJavaScriptTemplateForNewStoryFile` function in `code/lib/core-server/src/utils/new-story-templates/javascript.ts`:
    *   Accept an object with `basenameWithoutExtension`, `componentExportName`, `componentIsDefaultExport`, and `exportedStoryName`.
    *   Return a JavaScript story file string using default or named imports based on `componentIsDefaultExport`.

*   Implement the `getTypeScriptTemplateForNewStoryFile` function in `code/lib/core-server/src/utils/new-story-templates/typescript.ts`:
    *   Accept an object with `basenameWithoutExtension`, `componentExportName`, `componentIsDefaultExport`, `frameworkPackageName`, and `exportedStoryName`.
    *   Return a TypeScript story file string with appropriate imports and type annotations.

*   Implement the `getNewStoryFile` function in `code/lib/core-server/src/utils/get-new-story-file.ts`:
    *   Accept a `params` object with `componentFilePath`, `componentExportName`, `componentIsDefaultExport`, and an `options` object.
    *   Return an object with `exportedStoryName`, `storyFileContent`, and `storyFilePath`.
    *   Detect file type by extension and use the appropriate template generator.

*   Implement the `getStoryId` function in `code/lib/core-server/src/utils/get-story-id.ts`:
    *   Accept a `params` object with `storyFilePath` and `exportedStoryName`, and an `options` object.
    *   Return a story ID string in the format 'path-components--story-name'.
    *   Throw an error if `storyFilePath` does not match any configured story glob pattern.

*   Implement the `initCreateNewStoryChannel` function in `code/lib/core-server/src/server-channel/create-new-story-channel.ts`:
    *   Register a listener for the `CREATE_NEW_STORYFILE` event.
    *   On event, create the story file and emit `CREATE_NEW_STORYFILE_RESULT`.
    *   On success, emit `{ error: null, result: { storyId: string }, success: true }`.
    *   On failure, emit `{ error: 'An error occurred while creating a new story:\n{original error message}', result: null, success: false }`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.