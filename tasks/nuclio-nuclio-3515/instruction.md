Implement a feature in the release automation tool to persist resolved version information to a file. This will allow downstream CI/CD pipeline steps to access this information without re-running release logic.

*   Update the `Release` struct in `hack/scripts/releaser/releaser.go`:
    *   Add a new string field named `releaseInfoPath` to store the file path for saving release version information.

*   Implement the `saveReleaseInfo()` method for the `Release` struct:
    *   Signature: `func (r *Release) saveReleaseInfo() error`
    *   When `releaseInfoPath` is non-empty:
        *   Create or overwrite a file at the specified path.
        *   Write the following lines to the file:
            *   "CURRENT_VERSION: {currentVersion}\n"
            *   "TARGET_VERSION: {targetVersion}\n"
            *   "HELM_CHARTS_TARGET_VERSION: {helmChartsTargetVersion}\n"
        *   Ensure each version value is a standard semantic version string.
        *   Return nil on successful write.
    *   If `releaseInfoPath` is empty:
        *   Return nil without writing any file.
    *   Return a non-nil error if the file cannot be created or if writing fails.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.