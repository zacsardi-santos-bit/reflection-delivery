Implement a build-time version check for Storybook to eliminate runtime HTTP requests and improve update notification logic. Inject version data into the application bundle during server startup, allowing the client to access it immediately. Refine the notification system to avoid unnecessary alerts for patch updates and prerelease versions.

*   Modify the versions module to read version data from a build-time global variable `VERSIONCHECK`.
    *   `VERSIONCHECK` is a JSON string with a 'data' field containing 'latest' and 'next' version objects.
*   Implement the `initVersions` function with the following signature:
    *   `initVersions({ store, mode? }) -> { state, init, api }`
    *   Initialize state with versions from `VERSIONCHECK` data: `latest` and `next`.
    *   Return an object with `state`, `init`, and `api` containing version-related methods.
*   Ensure `initVersions` returns an initial state that includes:
    *   `versions.latest` and `versions.next` populated from `VERSIONCHECK`.
    *   No need for async operations or permanent persistence.
*   Implement the `api` object with methods:
    *   `getCurrentVersion()`: Returns the current installed version.
    *   `getLatestVersion()`: Returns the latest version from `VERSIONCHECK`.
    *   `versionUpdateAvailable()`: Determines if an update is available based on specific conditions.
*   Update `versionUpdateAvailable()` logic:
    *   Return false if the current and latest versions are the same.
    *   Return false if the latest version is only a patch increment above the current version.
    *   Return true for minor or major version increments.
    *   Return false if the latest version is a prerelease.
    *   Strip prerelease suffix from current version for comparison against the latest stable version.
    *   Return false if no latest version exists in state.
*   Refine update notification logic:
    *   Do not add notifications for patch-level changes or prerelease latest versions.
    *   Add notifications only if mode is not 'production', update is available, and not dismissed.
    *   On dismiss (onClear), call `store.setState` with `{ dismissedVersionNotification: latestVersionString }` and `{ persistence: 'permanent' }`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.