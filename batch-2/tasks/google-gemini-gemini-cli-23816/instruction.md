Implement a system to handle directory-specific ignore rules and enumerate ignored paths on disk. Update methods to distinguish between files and directories, and create utilities for path normalization.

*   Update the `getNormalizedRelativePath` function in `packages/core/src/utils/ignorePathUtils.ts`:
    *   Return `null` for invalid inputs: empty string, `null`, `undefined`, paths outside the project root, sibling directories sharing a prefix with the root, and Windows cross-drive absolute paths.
    *   Normalize paths to a forward-slash-separated relative path.
    *   Convert absolute paths inside the project root to relative equivalents.
    *   Append a trailing slash when `isDirectory` is true; strip any trailing slash when false.
    *   Return `'/'` for the project root when `isDirectory` is true and `''` when false.
    *   Convert Windows-style backslashes to forward slashes.

*   Update the `isIgnored` method in `packages/core/src/utils/gitIgnoreParser.ts` and `packages/core/src/utils/ignoreFileParser.ts`:
    *   Accept a second boolean parameter `isDirectory`.
    *   Return `true` for directory-specific patterns when `isDirectory` is true.
    *   Return `false` for directory-specific patterns when `isDirectory` is false.

*   Implement the `shouldIgnoreDirectory` method in `packages/core/src/services/fileDiscoveryService.ts`:
    *   Return `true` if the given directory path is ignored according to active ignore rules.

*   Implement the `getIgnoredPaths` method in `packages/core/src/services/fileDiscoveryService.ts`:
    *   Asynchronously return an array of absolute paths for ignored items on disk.
    *   Always include the `.git` directory when present.
    *   Do not traverse into already-ignored directories.
    *   Respect un-ignore patterns: exclude explicitly un-ignored paths.
    *   Accept an optional `FilterFilesOptions` argument to control which ignore rules are applied:
        *   `{ respectGitIgnore: false, respectGeminiIgnore: true }` returns paths ignored by gemini rules.
        *   `{ respectGitIgnore: true, respectGeminiIgnore: false }` returns paths ignored by git rules.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.