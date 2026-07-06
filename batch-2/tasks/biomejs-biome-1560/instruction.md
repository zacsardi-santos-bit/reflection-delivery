Fix the file filtering logic in Biome to ensure global ignore rules and VCS-based exclusions always take precedence over feature-level include lists. Ensure that override-level ignores only affect the specific override settings without impacting global file processing.

*   Ensure global file ignore rules under the top-level 'files' configuration always override feature-level include rules.
    *   A file matching a global ignore pattern must not be processed by any feature, even if included in a feature-level list.
*   Implement VCS ignore file exclusions (e.g., .gitignore) to prevent processing of files listed, regardless of feature-level includes.
    *   Ensure VCS-based exclusions have the same priority as global config-based exclusions.
*   Allow files with absolute paths that do not match any VCS ignore pattern to be processed normally without errors.
*   Ensure patterns in an override's 'ignore' field only affect the override's settings and do not prevent global processing.
    *   Files matched only by an override 'ignore' pattern must still be processed by the tool.
*   Evaluate the include/ignore cascade in the following order:
    *   Global 'files.include'/'files.ignore'
    *   VCS ignore file patterns
    *   Feature-level 'include'/'ignore' (formatter, linter, organizeImports)
    *   Only files passing all higher-priority filters are subject to feature-level filtering.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.