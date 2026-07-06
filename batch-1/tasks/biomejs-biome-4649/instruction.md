Improve the linter's handling of suppression comments by addressing two specific issues: redundant range suppressions and misplaced file-wide suppressions. Ensure that the linter correctly flags redundant suppressions and handles misplaced suppressions as ineffective.

*   Detect and handle redundant range suppressions:
    *   Identify when a file-level suppression (`biome-ignore-all`) already covers a specific lint rule for the entire file.
    *   Flag any range suppression (`biome-ignore-start/biome-ignore-end` pair) targeting the same rule as unused.
    *   Issue a 'suppressions/unused' diagnostic with the message: 'Suppression comment has no effect because another suppression comment suppresses the same rule.'
    *   Include an informational note pointing to the file-level suppression with the message: 'This is the suppression comment that was used.'
    *   Ensure the lint command exits successfully, counting the redundant range suppression as a warning.

*   Handle misplaced file-wide suppressions:
    *   Generate a 'suppressions/incorrect' diagnostic for each misplaced file-level suppression (`biome-ignore-all`) not at the file's beginning.
    *   Ensure misplaced suppressions do not suppress the targeted lint rules; report actual lint violations as errors.
    *   Ensure the lint command exits with an error status (non-zero exit code) when misplaced suppressions are present.
    *   Include a termination message indicating that errors were emitted.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.