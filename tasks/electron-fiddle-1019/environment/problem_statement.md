## Description

On Apple Silicon Macs, older versions of Electron do not have native ARM builds available for download. Specifically, ARM-compatible binaries only became available starting with version 11. Currently, the application has no awareness of this limitation — users on Apple Silicon Macs see active download buttons for all Electron versions, including older ones that simply don't have a downloadable ARM binary. Attempting to download such versions leads to confusion or failure.

## Expected Behavior

- The application should detect when it is running on an Apple Silicon Mac and automatically disable download controls for Electron versions that do not have ARM-compatible builds (i.e., versions below 11).
- Both the Electron settings panel (which lists available versions) and the version selector dropdown should reflect this: the download option for unsupported older versions should appear disabled.
- Users on Windows, Linux, or Intel Macs should be completely unaffected — all versions should remain downloadable as before.

## Why This Matters

Without this guard, users on Apple Silicon hardware have no in-app indication that certain older Electron versions cannot be downloaded for their machine. Adding architecture-aware download controls prevents confusion and makes the version management experience clearer for this growing class of users.
