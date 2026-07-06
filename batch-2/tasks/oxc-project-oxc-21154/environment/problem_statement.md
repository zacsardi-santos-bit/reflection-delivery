## Description

When using the formatter CLI and all explicitly-specified files are excluded by ignore rules, the tool gives a confusing and unhelpful error. The message only says that no target files were found, without any indication that the files may have been silently excluded by the active ignore configuration. This makes it very difficult for users to diagnose why their files aren't being processed.

Additionally, when using the "no error on unmatched pattern" mode with files that are all excluded by ignore rules, the tool currently exits silently (no progress output at all), which is inconsistent with the behavior shown for other code paths where the formatter at least announces that it started checking and finishes with a summary.

## Expected Behavior

- When all specified files are excluded by ignore rules and the tool exits with an error, the error message should explicitly mention that the matched files may have been excluded by ignore rules — not just say that no files were found.
- When running in check mode, the formatting progress header should appear in output even when the run ends with no files processed (due to ignore rules), just like it does for other execution paths.
- When using the unmatched-pattern-no-error mode and all files are excluded by ignore rules, the tool should show the standard progress and completion summary output (rather than producing no output at all), and the exit code should remain 0.

## Why This Matters

Users who are unaware of their ignore configuration — or who accidentally added a file to an ignore list — get no useful diagnostic information when their files are silently skipped. A clearer error message and consistent output behavior would help users quickly identify and resolve ignore configuration issues.
