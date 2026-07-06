## Description

When the database prunes obsolete table files from disk, it does not account for table files that are currently open by active readers. If a pruning operation runs while a table file is being read, it can delete that file from disk — causing the open reader to fail unexpectedly, even though it was legitimately opened before the prune started.

## Expected Behavior

- When pruning runs, any table file that was opened through the persister and has not yet been closed should be protected from deletion, regardless of whether it appears in the "keep" list.
- Table files that are not currently open and are not in the keep list should still be removed as usual.
- Opening a table file through the persister should register it so that concurrent pruning operations know to preserve it.

## Why This Matters

This is a correctness issue: a race between pruning and active reads can silently delete data that is currently in use. Any code that opens a table file and then reads from it should be able to complete successfully, even if pruning runs in between. Protecting open files from pruning prevents unexpected failures and potential data loss in concurrent workloads.
