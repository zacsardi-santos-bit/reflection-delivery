## Description

The commit log in M3DB automatically rotates files based on a configured block duration, but there is currently no way for callers to programmatically trigger a rotation at an arbitrary point in time. This limits the ability to coordinate operations such as snapshotting or flushing that require all writes up to a certain moment to be sealed in a completed, rotated commit log file.

## Expected Behavior

- A new method should be available on the commit log that explicitly forces a rotation of the current log file.
- When rotation is triggered, the method should return metadata about the resulting file, including its start time, duration, index, and file path.
- The rotation must be safe to invoke while writes are occurring concurrently.
- Data written before a rotation must be preserved and remain readable after the commit log is closed.
- Calling the rotation method on a closed commit log should return an appropriate closed error rather than panicking or silently failing.

## Why This Matters

Without on-demand rotation, callers must rely solely on time-based rotation driven by block size, making it impossible to guarantee data locality relative to an external operation. Having explicit rotation control allows snapshot and flush workflows to precisely bound the data they need to recover.
