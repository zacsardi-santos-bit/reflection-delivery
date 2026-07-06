## Description

There is a regression in the cherry-pick workflow. After pasting a set of cherry-picked commits onto a branch, attempting to use range selection to copy additional commits from another branch does not work correctly. The range-copy operation either silently fails or produces incorrect results, making it impossible to cherry-pick multiple commits in sequence across paste operations.

## Expected Behavior

- After pasting cherry-picked commits, the status bar should no longer show "commits copied" — the paste is done, so there's nothing in the clipboard from the user's perspective
- After a paste, the user should be able to navigate to another branch, select a range of commits, mark them for cherry-picking, and see the correct count displayed in the status bar
- Pasting the newly selected range of commits should work correctly, producing the expected commit ordering on the target branch

## Steps to Reproduce

1. Cherry-pick a single commit from a source branch's commit list
2. Switch to the target branch's commits view and paste
3. After paste, navigate back to the source branch commit list
4. Use range selection to mark multiple commits for cherry-picking
5. Paste the range onto the target branch

The range paste either fails or produces incorrect commit ordering.

## Why This Matters

Users who regularly cherry-pick commits across branches often need to perform multiple cherry-pick operations in sequence. If the first paste corrupts the state for subsequent range-based cherry-picks, they must restart the tool or work around the bug, which is disruptive to the workflow.
