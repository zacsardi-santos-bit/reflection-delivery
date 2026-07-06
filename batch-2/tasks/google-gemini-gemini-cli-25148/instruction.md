Extend the skill inbox in the CLI to support both new skills and updates to existing skills. Implement functionality to propose, review, and apply incremental updates to already installed skills using patch files. Ensure that the system respects workspace trust settings and provides a safe, atomic update process.

*   Implement `listInboxPatches` function:
    *   Return an empty array if the skills memory directory does not exist or contains no valid .patch files.
    *   Return an array of `InboxPatch` objects, each with `fileName`, `name`, `entries`, and `extractedAt` fields.

*   Implement `applyInboxPatch` function:
    *   Validate `fileName` to ensure it contains no path separators.
    *   Perform a dry-run before applying patches; commit changes only if all patches apply cleanly.
    *   Handle multi-section patches and strip git-style prefixes from diff headers.
    *   Return specific error messages for various failure modes, including invalid filenames, non-existent files, and path traversal attempts.
    *   Ensure atomic application of patches and rollback on failure.

*   Implement `dismissInboxPatch` function:
    *   Delete the specified .patch file and return a success message.
    *   Return error messages for missing files or invalid filenames.

*   Implement `validatePatches` function:
    *   Return an empty array if the directory does not exist or contains no valid patches.
    *   Delete invalid patches and return filenames of valid ones.

*   Update the `SkillInboxDialog` component:
    *   Fetch both new skills and patches on mount.
    *   Display section headers for 'New Skills' and 'Skill Updates' when both are present.
    *   Provide a preview and options to apply or dismiss patches, respecting workspace trust settings.

*   Ensure `startMemoryService` emits a notification when new patches are created, but not when no new patches are added.

*   Update `InboxSkill` interface to include a `content` field for preview purposes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.