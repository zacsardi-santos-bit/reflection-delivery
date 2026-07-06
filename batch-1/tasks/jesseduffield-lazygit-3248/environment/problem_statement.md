## Description

The files panel in lazygit has two usability gaps that this issue tracks:

1. **Confusing discard menu title**: When pressing the discard key on a file, directory, or linked worktree, the confirmation popup shows the *name of the item* as its title (e.g. "dir", "file-one", "linked-worktree"). This is confusing because the title describes *what* is selected, not *what action* you're about to take. It should instead show something that communicates the action, like "Discard changes".

2. **No multi-file range selection for stage/discard**: Currently, staging, unstaging, and discarding changes can only be done one file at a time. Other panels in lazygit support range selection (selecting multiple items and acting on them all at once), but the files panel does not. Users who want to stage or discard several files have to repeat the action for each one individually.

## Expected Behavior

- The discard confirmation menu should always display a consistent, action-oriented title rather than the item name.
- Users should be able to use the range-select toggle to span a selection across multiple files and directories in the files panel, then stage, unstage, or discard changes for all of them at once.
- Collapsed directory nodes should be included in range selections, operating on all files within.
- If the selection includes a submodule, the discard action should be blocked with a clear notification explaining that range selection is not supported for submodules.

## Why This Matters

Power users regularly need to stage or discard changes across multiple files — for example, cleaning up a working directory or staging a coherent set of changes. Having to act on each file one at a time is tedious when other panels already support bulk operations.
