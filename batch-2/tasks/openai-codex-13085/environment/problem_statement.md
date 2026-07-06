## Description

There is currently no way to reset the local memory system to a clean state from the command line. When accumulated memories become stale, corrupted, or otherwise need to be wiped, a developer has to manually delete database records and filesystem directories — which is tedious and error-prone.

## Expected Behavior

A debug subcommand should be added that resets memory state in a single step:

- Removes all accumulated memory summaries stored in the database
- Removes any pending or completed memory background jobs from the database
- Marks all existing sessions as memory-disabled so historical rollouts are not picked up again
- Deletes the memories directory on disk (under the configured home directory)
- Prints a confirmation message to the terminal indicating the memory state was cleared

## Why This Matters

Developers troubleshooting memory-related issues or wanting a fresh start need a reliable, built-in command to wipe all memory data at once. Without this, there is no safe or supported way to do so, and partial manual cleanups can leave the system in an inconsistent state.
