## Description

There are two separate issues causing test failures in this implementation of core Unix utilities.

**Issue 1 — WSL compatibility in disk-usage and group-change tests**

When running the test suite under Windows Subsystem for Linux, two categories of tests fail:

- Disk usage tests compare expected block counts that differ on WSL. WSL reports fewer blocks for subdirectories and symbolic/hard links than a standard Linux kernel does.
- The group-change command's reference test produces unexpected output on WSL because of a known WSL bug: non-root users are incorrectly granted elevated privileges for ownership operations, causing the command to succeed where it should not (or succeed differently). The test should be skipped on WSL, just as it is already skipped for root users.

A common utility to detect the WSL environment is needed in the test helpers so that individual tests can branch on it.

**Issue 2 — Wrong file birth/creation timestamp in the file status utility**

The file status utility reports incorrect values for the birth/creation time format specifiers. The implementation appears to be computing *elapsed time since the file was created* rather than the *actual creation timestamp relative to the Unix epoch*. As a result, both the human-readable creation time and the creation time expressed in seconds disagree with what the system's own stat command reports.

## Expected Behavior

- A test helper function that returns whether the current process is running under WSL.
- Disk usage tests use WSL-appropriate expected output when running under WSL.
- The group-change reference test is skipped on WSL.
- The stat utility's birth/creation time output matches the system stat command when birth time is available.
- When birth time is not supported by the kernel or filesystem, the stat utility outputs the conventional "unknown" sentinel values that the system stat also uses.

## Why This Matters

Without these fixes, a significant portion of the test suite produces false failures on WSL and on any environment that exercises file creation-time reporting, making it difficult to verify correctness of the implementation on those platforms.
