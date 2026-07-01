Improve the usability of the files panel in lazygit by updating the discard confirmation menu title and adding range selection support for staging, unstaging, and discarding files. Ensure the interface and test cases reflect these changes.

*   Update the discard confirmation menu:
    *   Display 'Discard changes' as the title when discarding any item (file, directory, or linked worktree) in the files panel.
    *   Retain the submodule's name as the title when discarding a single submodule without range selection.

*   Implement range selection in the files panel:
    *   Allow users to select multiple files and directories using a range-select toggle key.
    *   Enable staging or unstaging of all selected items using the primary action key.
        *   If any item in the range has unstaged changes, stage all items; otherwise, unstage all.
    *   Include all files within collapsed directories when selected in a range.
    *   Allow discarding all changes for selected items with the discard key.
        *   Show 'Discard changes' as the menu title with a 'Discard all changes' option.
    *   Allow discarding only unstaged changes for selected items.
        *   Show 'Discard changes' as the menu title with a 'Discard unstaged changes' option.
        *   Fully remove untracked files with no staged changes; retain files with only staged changes.
    *   Block discard actions when a range selection includes a submodule.
        *   Display a toast notification: 'Disabled: Range select not supported for submodules'.

*   Update navigation for files in merge conflict state:
    *   Use the Enter key to navigate to the merge conflicts view.

*   Modify the test suite:
    *   Include integration test cases: DiscardRangeSelect, DiscardUnstagedRangeSelect, DiscardVariousChanges, DiscardVariousChangesRangeSelect, and StageRangeSelect.
    *   Remove the DiscardChanges test case.
    *   Use the helper function `createAllPossiblePermutationsOfChangedFiles` in `pkg/integration/tests/file/shared.go` for setting up permutations in DiscardVariousChanges and DiscardVariousChangesRangeSelect tests.

*   Ensure the discard popup menu title is 'Discard changes' for all items except single submodule actions.

*   Display the toast notification with the message: 'Disabled: Range select not supported for submodules' when applicable.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.