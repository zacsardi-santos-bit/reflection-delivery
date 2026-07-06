## Description

The Nx release system currently has no explicit configuration option to control whether changes are pushed to a remote git repository after versioning or changelog generation. This makes the behavior opaque and prevents users from opting out of the push step entirely through configuration. There is also no validation to catch the contradictory case where a user enables automatic release creation on a hosting platform (which requires pushing to a remote) but has also explicitly disabled git pushing — this leads to a silent failure or confusing error at runtime rather than an early, clear configuration error.

## Expected Behavior

- The release git configuration should include an explicit push option (boolean) that controls whether the release process pushes commits and tags to the remote git repository.
- The push option should default to disabled unless a hosting-platform release creation (e.g., GitHub Releases) is configured, in which case it should automatically default to enabled since pushing is a prerequisite for creating such a release.
- When a user explicitly disables pushing alongside an enabled hosting-platform release creation, the configuration resolution step should return a clear error rather than proceeding to a runtime failure. The error should indicate a conflict between the disabled push setting and the enabled hosting-platform release creation.
- This validation should apply regardless of which git configuration scope the push-disabled setting appears in (top-level, version-specific, or changelog-specific).
- When the system does push to a remote, the log output should include the specific remote name being used so users can confirm which remote is receiving the changes.
- The "matched projects" informational message shown when using project/group filters should appear once at the start of a release run rather than being repeated at each sub-step.

## Why This Matters

Users who manage their own push step (e.g., in CI) or who want to run a dry-run-style release without pushing need a supported way to disable git push. Without an explicit option, the system either always pushes or has undocumented behavior. The contradictory-config validation prevents hard-to-debug runtime errors for users who misunderstand the dependency between hosting releases and git pushing.
