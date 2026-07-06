Correct the demo link in the documentation to point to the main branch and implement an automated check to ensure future links follow the correct format. Update the import syntax and file path prefix in a test utility file for compatibility.

*   Ensure all StackBlitz URLs in the following files have the correct GitHub path:
    *   packages/*/README.md
    *   packages/lexical-website/docs/**/*.md (excluding packages/lexical-website/docs/api/**)
    *   URLs must start with: 'https://stackblitz.com/github/facebook/lexical/tree/main/examples/...'
*   Correct the StackBlitz URL in packages/lexical-website/docs/collaboration/react.md:
    *   Ensure it points to 'facebook/lexical/tree/main/examples/' instead of a different branch.
*   Update the import statement in packages/lexical-playground/__tests__/utils/index.mjs:
    *   Use namespace import syntax: `import * as glob` for the glob module.
*   Modify the file path prefix in the findAsset function in packages/lexical-playground/__tests__/utils/index.mjs:
    *   Use 'packages/lexical-playground/build' without a leading './'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.