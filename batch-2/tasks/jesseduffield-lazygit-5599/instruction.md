I've found a regression in the cherry-pick workflow.

*   After a user pastes cherry-picked commits onto a branch, the information bar must NOT display any 'commits copied' message, reflecting that the paste operation has completed

*   After a paste operation, the user must be able to navigate to another branch's commit list, use range selection to mark multiple commits for cherry-picking, and the information bar must show the correct count (e.g., '3 commits copied') after confirming the range copy

*   When pasting a range of cherry-picked commits (e.g., 3 commits) after a previous paste, the confirmation dialog must reflect the correct count: 'Are you sure you want to cherry-pick the 3 copied commit(s) onto this branch?'

*   After pasting a range of commits (e.g., four, three, two) that were range-selected after a prior paste, the commits view must list them in the correct order above any previously pasted commits, resulting in: four, three, two, five, base


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.