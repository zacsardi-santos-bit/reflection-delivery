Update the project to replace the outdated Git URL parsing library with a modern alternative. Ensure all existing functionality remains intact and update the module configuration accordingly.

*   Replace the old library:
    *   Update the project's module dependency list to declare `github.com/chainguard-dev/git-urls` as a direct dependency, replacing `github.com/whilp/git-urls`.
    *   Modify all source files to import `github.com/chainguard-dev/git-urls` instead of `github.com/whilp/git-urls`, maintaining the alias `giturls`.

*   Update module files:
    *   Modify `go.mod` and `go.sum` to include the new library and remove the old one, ensuring the module graph remains consistent.
    *   Verify that the `deps` package compiles successfully with the new library.

*   Ensure existing functionality:
    *   Confirm that `getRepoNameFromGitURL` continues to parse:
        *   HTTPS URLs with and without trailing slashes and `.git` extensions.
        *   SSH URLs with and without `.git` extensions.
        *   Handle edge cases like missing repo names, invalid URLs, and empty strings.
    *   Maintain dependency timeout resolution logic:
        *   Return the specific dependency timeout when set.
        *   Fall back to the default timeout when only it is set.
        *   Return a zero value when both are unset.
    *   Ensure YAML unmarshaling of manifest dependencies works for both array-form and map-form representations.
    *   Verify variable expansion in dependency configuration functions correctly.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.