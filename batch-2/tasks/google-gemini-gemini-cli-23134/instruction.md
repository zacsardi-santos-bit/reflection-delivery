Update the memory discovery process to ensure context files at the git repository root are included when traversing from a trusted root. Implement the following changes to the `getEnvironmentMemoryPaths` and `loadJitSubdirectoryMemory` functions to extend the search to the git root, identified by a `.git` directory or file.

*   Modify `getEnvironmentMemoryPaths` in `packages/core/src/utils/memoryDiscovery.ts`:
    *   Traverse upward from each trusted root to the git root, collecting memory files from all directories along the path.
    *   Recognize the git root by the presence of a `.git` directory or file.
    *   If no git root is found, use the trusted root as the traversal ceiling.
    *   Ensure files above the git root are not included.

*   Modify `loadJitSubdirectoryMemory` in `packages/core/src/utils/memoryDiscovery.ts`:
    *   Extend traversal from the deepest matching trusted root up to the git root, collecting memory files from all directories in that range.
    *   Recognize the git root by the presence of a `.git` directory or file.
    *   If no git root is found, use the trusted root as the traversal ceiling.
    *   Handle multiple nested trusted roots by starting from the innermost root and extending to the git root.

*   Ensure both functions recognize a `.git` file (used in submodules and worktrees) as a valid git root indicator, stopping traversal at its parent directory.

*   Maintain existing behavior when no git repository is detected, stopping at the trusted root.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.