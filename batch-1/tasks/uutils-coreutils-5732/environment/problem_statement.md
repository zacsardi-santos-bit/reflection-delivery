## Description

The head command fails when using a negative count argument (to print all content except the last N bytes or lines) on special virtual filesystem files in Linux, such as those found under `/proc`. These files are generated on-the-fly by the kernel, report a size of zero, and do not support seeking. The current implementation assumes it can seek to determine file bounds before reading backwards, which causes the command to fail or produce no output for these files.

## Expected Behavior

- Running head with a negative byte count on a Linux virtual filesystem file (such as /proc/version) should succeed and produce the expected content.
- When seeking is not possible, the command should gracefully handle the file by reading its content sequentially and outputting all but the last N bytes or lines.
- The error message shown when the user provides a count too large for the platform should describe both bytes and lines modes as possible causes of the overflow — not just bytes.

## Why This Matters

Users who want to view the tail-excluding content of kernel-provided pseudo-files using negative count syntax get failures or empty output, which is inconsistent with GNU coreutils behavior. The error message improvement also clarifies what causes the overflow so users understand both modes can trigger it.
