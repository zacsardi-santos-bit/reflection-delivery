I'm poking at the files panel in lazygit and there's two things bugging me that I want fixed together.

First off, when I hit the discard key on something in the files panel the confirmation popup uses the selected item's name as its title, so I see things like "dir" or "file-one" or "linked-worktree" staring back at me. That's confusing because it tells me what's selected, not what I'm about to do. I want that menu to always show an action-oriented title like "Discard changes" no matter what's selected. The one exception is single submodule actions, those can keep showing the submodule name like they do now.

Second, I want range selection in the files panel for staging, unstaging, and discarding. Other panels already let you toggle range select, move to cover several items, and act on all of them, but the files panel is stuck acting on one file at a time. So I should be able to toggle range select, navigate over several files and directories, then press the stage key or discard key and have it apply to everything in the range. Collapsed directories need to work too, if a collapsed dir is in the selection it should act on all its contents.

One edge case: if the range includes a submodule, discard shouldn't go through. Instead pop a notification saying range selection isn't supported for submodules and they need to act on the submodule individually.

Oh and since the primary action key (space) is now doing staging, navigating into the merge conflicts view for conflict-status files should move over to the Enter key instead of space.

This is mostly about power users who clean up or stage coherent chunks across a bunch of files, doing it one at a time is tedious. Relevant code lives around the files controller and context in lazygit's gui packages.
