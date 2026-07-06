## Description

The flutter tools codebase contains a constant representing the OS-level "file not found" error code, but its name is ambiguous — it describes a high-level concept rather than making clear that it is a numeric OS error code. This can confuse contributors who encounter it and aren't immediately sure what kind of value it holds. The constant should be renamed to make its nature explicit.

Additionally, there is a gap in test coverage: when the flutter tool attempts to delete a file or directory on Windows and receives an OS-level "file not found" error — but the entity still exists on disk — the expected behavior is that the tool exits with a friendly, actionable message rather than surfacing a raw system exception. This behavior is correct in the current implementation but is not covered by any test, leaving it unprotected against future regressions.

## Expected Behavior

- The constant previously used for OS error code 2 should be renamed to better signal that it holds a numeric OS error code.
- All existing usages of the old constant name throughout the source should be updated to the new name.
- The file deletion helper must throw a tool exit (with a user-friendly message) when a Windows delete operation fails with OS error code 2 and the target file or directory still exists afterward.

## Why This Matters

The rename improves code clarity and reduces confusion for contributors. The new test ensures the critical error-handling behavior on Windows is documented and protected against regressions, so users always receive a clear, actionable message instead of a cryptic system error when file deletions fail unexpectedly.
