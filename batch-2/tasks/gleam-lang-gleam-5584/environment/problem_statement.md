## Description

The "check outdated dependencies" command in the Gleam build tool currently only produces output when packages have newer versions available. When all dependencies are already up to date, the command exits silently — giving no indication whether it ran successfully or simply found nothing to report.

## Expected Behavior

- When outdated packages are found, the output should always begin with a summary showing how many packages out of the total have newer versions available (e.g., "1 of 12 packages have newer versions available."), followed by the existing formatted table of outdated packages.
- When no packages are outdated, the command should still print a summary line (e.g., "0 of 12 packages have newer versions available.") so the user knows the check completed successfully.

## Why This Matters

Without a summary message, users running the outdated check on a fully up-to-date project have no way to confirm that the command actually ran and checked all packages. The new summary makes the command's result unambiguous in all cases.
