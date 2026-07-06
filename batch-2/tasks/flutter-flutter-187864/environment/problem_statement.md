## Description

The Flutter build tools have several gaps in error handling and filesystem robustness that cause poor developer experiences.

**File-not-found errors are not user-friendly.** When a file or directory cannot be found during a build operation on Linux, macOS, or Windows, the tool sometimes propagates a raw, low-level filesystem exception instead of a clear actionable message. Developers are left with a cryptic error and no guidance on how to recover.

**Windows builds are not resilient to transient file locking.** On Windows, it is common for antivirus software or other tools to briefly hold a lock on files during build operations. Currently, the first lock conflict immediately aborts the operation with an error. The tool should automatically retry a limited number of times before giving up, and when it does give up, it should provide a clear message that the file is in use.

**Swift Package Manager symlink creation is fragile.** When building Flutter apps with plugins on Apple platforms, the tool creates symlinks to plugin Swift packages. If a previous interrupted build left a stale file, directory, or broken symlink at the expected symlink location, the current build crashes. The tool should handle all of these pre-existing conditions gracefully by cleaning them up before creating the symlink.

## Expected Behavior

- "File or directory not found" OS errors on all platforms should produce a clean, actionable tool exit message
- On Windows, transient file-locking errors during write operations should trigger automatic retries before failing
- If all retries are exhausted due to locking, the tool should report that the file is in use with a helpful message
- Swift Package Manager symlink creation should succeed even when a directory, file, or broken symlink already exists at the target path
- During parallel Xcode builds, if two processes race to create the same symlink and one succeeds first, the other should detect the already-correct symlink and continue rather than failing

## Why This Matters

These issues cause confusing build failures that are hard to diagnose and recover from. By providing clear error messages and automatic retries, the tool becomes more reliable and easier to use, especially on Windows and in complex multi-target Xcode setups.
