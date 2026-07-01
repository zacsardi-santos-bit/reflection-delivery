I'm working on improving the files panel in lazygit and need two related changes made.

First, the discard confirmation menu currently shows the name of the selected item (file, directory, or linked worktree) as its title. This is a bit confusing because the title tells you *what* is selected rather than *what you're about to do*. I'd like it to always show a clear, action-oriented title like "Discard changes" instead, regardless of what item is selected. The only exception should be single submodule actions, which can keep using the submodule name.

Second, I want to add range selection support to the files panel for staging, unstaging, and discarding. Right now you can only act on one file at a time, but other panels in lazygit already support selecting a range of items and acting on all of them at once. I'd like the same to work in the files panel: a user should be able to toggle range select, navigate to cover several files (and directories), then press the stage key or discard key to apply the action to all selected items. Collapsed directories should work too — including them in the selection should act on all their contents.

One edge case: if the range selection includes a submodule, the discard action should not proceed. Instead, it should show a notification telling the user that range selection is not supported for submodules, and they need to act on the submodule individually.

Also, as part of this change, navigating to the merge conflicts view for conflict-status files should use the Enter key rather than the primary action key (space), since the primary action key is now used for staging.
