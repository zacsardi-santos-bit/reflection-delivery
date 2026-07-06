Implement a consent check for the browser automation feature to ensure users are informed about data collection practices before the browser agent starts. Respect existing user preferences regarding usage statistics by adjusting the browser process launch accordingly.

*   Implement the `getBrowserConsentIfNeeded` function in `packages/core/src/utils/browserConsent.ts` with the following behavior:
    *   Return `true` immediately if the consent sentinel file exists at the path derived from `Storage.getGlobalGeminiDir()` with the filename 'browser-consent-acknowledged.txt'.
    *   In non-interactive mode (no listeners on `coreEvents`):
        *   Automatically accept consent by writing the sentinel file with content 'consent acknowledged'.
        *   Return `true` without emitting a consent request.
    *   In interactive mode (listeners on `coreEvents`):
        *   Call `coreEvents.emitConsentRequest` with a payload containing:
            *   A `prompt` string including 'Privacy Notice' and the URL 'policies.google.com/privacy'.
            *   An `onConfirm` callback function.
        *   On user acceptance (`onConfirm` called with `true`):
            *   Write the sentinel file with content 'consent acknowledged'.
            *   Return `true`.
        *   On user declination (`onConfirm` called with `false`):
            *   Do not write the sentinel file.
            *   Return `false`.

*   Update `BrowserManager.ensureConnection()` to:
    *   Call `getBrowserConsentIfNeeded` before proceeding.
    *   Throw an error if `getBrowserConsentIfNeeded` returns `false` (consent declined), preventing the browser from launching.

*   Adjust the browser process launch in `BrowserManager` based on `usageStatisticsEnabled` configuration:
    *   If `usageStatisticsEnabled` is `false`, pass the flags `--no-usage-statistics` and `--no-performance-crux` to the spawned browser MCP process.
    *   If `usageStatisticsEnabled` is `true` (or default), do not pass these flags.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.