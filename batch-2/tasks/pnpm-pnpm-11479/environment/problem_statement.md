## Description

The pnpm release tooling needs a set of utility functions for managing changeset files across branches. Currently there is no module handling the bookkeeping of which changesets have already been released, nor any mechanism to temporarily hide or permanently remove those changeset files during the release process.

## Expected Behavior

A new utility module should be introduced that provides the following capabilities:

- **Branch-to-filename conversion**: Given a git branch name (which may contain slashes), produce a safe, flat filename for use on disk by substituting slashes with dashes and appending a `.txt` extension.
- **Reading released IDs**: Given a directory containing per-branch tracking files, read all the changeset IDs that have been recorded as released across all branches, merging them into a single deduplicated collection. Lines that are comments or empty should be ignored. If the directory does not exist, return an empty result without error.
- **Appending released IDs**: Record newly released changeset IDs into the appropriate per-branch tracking file, deduplicating against any IDs already on disk and keeping the file sorted. If no IDs are provided, the file should not be created. The target directory should be created automatically if it is missing.
- **Hiding released changeset files**: Temporarily rename released changeset markdown files in a directory so that release tooling skips them. If any rename fails partway through, all previous renames in the same operation must be rolled back so the directory is left in a consistent state.
- **Restoring hidden changesets**: Rename the hidden files back to their original names (reversing the hide operation).
- **Deleting hidden changesets**: Permanently remove the hidden files once a release is confirmed.
- **Listing pending changeset IDs**: Return a sorted list of changeset IDs present in a directory, excluding the standard readme file and any non-changeset files.

## Why This Matters

Without this utility, the release pipeline has no reliable way to track which changesets have been published on a given branch, leading to potential double-releases or incorrect processing of already-released changesets. The hide/restore/delete mechanism also allows the release tooling to operate on an isolated view of the pending changesets without permanently modifying the repository until the release is confirmed.
