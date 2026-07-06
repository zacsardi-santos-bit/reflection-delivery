Implement a mechanism to notify users of available updates for GitHub CLI extensions. Ensure the system respects user preferences and environmental constraints, such as CI environments, and provides a pluggable update-checking function.

*   Implement `CheckForExtensionUpdate` in `internal/update/update.go`:
    *   Accept parameters: `em` (extensions.ExtensionManager), `ext` (extensions.Extension), `now` (time.Time).
    *   Return `nil` for local extensions without creating a state file.
    *   For git and binary extensions with updates, return a `*ReleaseInfo` containing the latest version and URL.
    *   Write a state file at `em.UpdateDir(ext.Name())+'/state.yml'` with check time and latest release.
    *   If a state file exists and is less than 24 hours old, return `nil` without further checks.
    *   Refresh and return update info if the state file is 24 or more hours old.

*   Define `StateEntry` struct in `internal/update/update.go`:
    *   Include fields `CheckedForUpdateAt` (time.Time) and `LatestRelease` (ReleaseInfo).

*   Implement `setStateEntry` in `internal/update/update.go`:
    *   Write a `StateEntry` to a specified path, creating parent directories as needed.

*   Implement `getStateEntry` in `internal/update/update.go`:
    *   Read and return a `StateEntry` from a specified file path.

*   Implement `ShouldCheckForUpdate` in `internal/update/update.go`:
    *   Return `false` if any of these environment variables are set: `GH_NO_UPDATE_NOTIFIER`, `CODESPACES`, `CI`, `BUILD_NUMBER`, `RUN_ID`.

*   Implement `ShouldCheckForExtensionUpdate` in `internal/update/update.go`:
    *   Return `false` if any of these environment variables are set: `GH_NO_EXTENSION_UPDATE_NOTIFIER`, `CODESPACES`, `CI`, `BUILD_NUMBER`, `RUN_ID`.

*   Update `ExtensionManager` interface in `pkg/extensions/manager.go`:
    *   Include `UpdateDir(name string) string` method to return the update state directory path.

*   Modify `Manager` struct in `pkg/cmd/extension/manager.go`:
    *   Add `updateDir` field of type `func() string`.
    *   Implement `UpdateDir(name string) string` to use `updateDir` for path construction.

*   Update `Manager.Remove` method:
    *   Delete the extension's update state directory in addition to its data directory.

*   Update `Manager.Install` and `Manager.InstallLocal` methods:
    *   Remove any pre-existing update state directory for the extension being installed.

*   Implement `normalizeExtension` in `pkg/cmd/extension/manager.go`:
    *   Return the name unchanged if it starts with 'gh-'; otherwise, prepend 'gh-' and return.

*   Update `NewCmdExtension` in `pkg/cmd/root/extension.go`:
    *   Accept a fourth parameter: a function with signature `func(extensions.ExtensionManager, extensions.Extension) (*update.ReleaseInfo, error)` for checking updates.

*   Ensure extension upgrade output displays names without padding.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.