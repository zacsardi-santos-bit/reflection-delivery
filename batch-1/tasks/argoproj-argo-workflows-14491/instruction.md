Develop a feature generation tool to streamline the documentation of new features for contributors and maintainers. Ensure the tool can create, validate, compile, and archive feature announcement files, supporting both preview and final release modes.

*   Implement the `feature` struct with the fields: `Component`, `Description`, `Author`, `Issues`, and `Details`.
*   Initialize `timeNow` as a package-level variable of type `func() time.Time`, defaulting to `time.Now`.
*   Define string-valued constants or variables for `templateFile`, `pendingDir`, `docsOutput`, and `featuresDir` to represent file paths.
*   Implement `parseContent(source string, content string) (bool, feature, error)` to:
    *   Return `(true, feature, nil)` for valid content with recognized components and issues.
    *   Return `(false, feature, nil)` for invalid content, such as missing issues or unrecognized components.
    *   Extract issue numbers from the `Issues:` line, ignoring any '#' prefix.
    *   Populate `Details` with content following the first blank line after metadata.
*   Recognize component names: 'General', 'UI', 'CLI', 'CronWorkflows', 'Telemetry', 'Build and Development'.
*   Implement `format(version string, features []feature) string` to:
    *   Start with a header `# New features in {version} ({YYYY-MM-DD})\n\n`.
    *   Include "This is a concise list of new features.\n\n" after the header.
    *   Group features by `Component`, formatting each with description, author, and issue links.
    *   Include `Details` with a two-space indent, ensuring a blank line follows each feature entry.
*   Implement `newFeature(filename string) error` to:
    *   Create a file at `pendingDir/{filename}.md` using `templateFile`.
    *   Default to "new-feature.md" if `filename` is empty.
    *   Return an error for filenames with invalid characters like '/' or '@'.
*   Implement `loadFeatureFile(filePath string) (bool, feature, error)` to:
    *   Return an error if the file does not exist.
    *   Extract issue numbers without '#' prefixes.
    *   Return `(true, feature, nil)` for valid files and `(false, feature, nil)` for invalid ones.
*   Implement `updateFeatures(dryRun bool, version string, final bool) error` to:
    *   Format and write output to `docsOutput` unless `dryRun` is true.
    *   Move files to `featuresDir/released/{version}/` if `final` is true and `version` is non-empty.
    *   Return an error on failure.
*   Implement `validateFeatures() error` to:
    *   Validate all files in `pendingDir`.
    *   Return an error if any file is invalid.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.