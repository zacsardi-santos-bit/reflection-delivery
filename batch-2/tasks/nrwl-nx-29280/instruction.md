Implement explicit control over whether git changes are pushed to a remote repository during the release process in the Nx release configuration system. Add a `push` option to the git configuration, defaulting to off unless a hosting-platform release is configured. Ensure validation catches conflicts between disabled push settings and enabled hosting-platform releases.

*   Update the `NxReleaseGitConfiguration` interface:
    *   Add an optional `push?: boolean` field.

*   Modify `createNxReleaseConfig` function:
    *   Include a `push` boolean field in resolved git configuration objects.
    *   Default `push` to `false` unless `createRelease: 'github'` is configured, in which case default to `true`.
    *   Return an error object with code `'GIT_PUSH_FALSE_WITH_CREATE_RELEASE'` and `data: {}` if `push: false` is set and `createRelease: 'github'` is enabled in any changelog. Set `nxReleaseConfig` to `null`.

*   Extend `CreateNxReleaseConfigError`:
    *   Add `'GIT_PUSH_FALSE_WITH_CREATE_RELEASE'` to the `code` union type.

*   Adjust logging for git operations:
    *   Include the specific remote name in log messages when pushing, e.g., `Pushing to git remote "origin"`.

*   Refactor `filterReleaseGroups` function:
    *   Return a `filterLog` field (`{ title: string; bodyLines: string[] } | null`) instead of calling `output.note()` directly.
    *   Ensure all error paths return `filterLog: null`.
    *   Prevent inline emission of project filter match messages during sub-steps. Return via `filterLog` and allow optional suppression via an internal environment variable.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.