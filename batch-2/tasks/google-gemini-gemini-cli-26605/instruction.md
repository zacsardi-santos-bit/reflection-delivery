Implement a solution to ensure memory file paths maintain their original casing, dynamically manage memory command subcommands based on configuration, and handle malformed memory patches silently. Update the necessary functions and interfaces according to the requirements below.

*   Implement `toAbsolutePath` in `packages/core/src/utils/paths.ts`:
    *   Resolve relative paths to absolute paths.
    *   Convert all backslashes to forward slashes.
    *   Preserve original letter casing of path segments on all platforms.
    *   Ensure paths are not lowercased.

*   Update `getUserProjectMemoryPaths` in `packages/core/src/utils/memoryDiscovery.ts`:
    *   Use `toAbsolutePath` to preserve on-disk casing.
    *   Return a single-element array with the path to `PROJECT_MEMORY_INDEX_FILENAME` if it exists.
    *   Fall back to `DEFAULT_CONTEXT_FILENAME` if `PROJECT_MEMORY_INDEX_FILENAME` is absent.
    *   Return an empty array if neither file exists.

*   Modify `memoryCommand` in `packages/cli/src/ui/commands/memoryCommand.ts`:
    *   Change from a static `SlashCommand` object to a function accepting a `Config` object or null.
    *   Import `Config` type from the core config module.
    *   Exclude the 'add' subcommand when `isMemoryV2Enabled()` returns true.
    *   Include the 'add' subcommand when `config` is null or `isMemoryV2Enabled()` returns false.

*   Enhance `startMemoryService` in `packages/core/src/services/memoryService.ts`:
    *   Validate and remove malformed memory inbox patches before recording state or emitting feedback.
    *   Define a malformed patch as one where the actual number of added lines in a hunk exceeds the count declared in the diff hunk header.
    *   Delete malformed patch files silently without emitting feedback.
    *   Record empty `memoryCandidatesCreated` and `memoryFilesUpdated` arrays when only malformed patches are encountered.

*   Update `getEnvironmentMemoryPaths` in `packages/core/src/utils/memoryDiscovery.ts`:
    *   Use `toAbsolutePath` to preserve case-distinct files.
    *   Ensure that on case-insensitive filesystems, differently-cased paths resolving to different inodes both appear in the result.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.