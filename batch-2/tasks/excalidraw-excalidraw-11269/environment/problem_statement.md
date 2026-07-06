## Description

The element ordering normalization logic has a bug in how it handles elements belonging to nested subgroups that are interleaved with sibling elements of a shared parent group.

When a user has canvas elements where some belong only to a top-level group (e.g., group A) and others belong to nested subgroups within that same parent group (e.g., group A → B → C), and these elements appear in mixed order, the normalization routine incorrectly relocates all the deeper-subgroup elements to the end of the parent group block. The correct behavior is to preserve the first-occurrence position of each distinct inner group.

## Expected Behavior

- When a sub-group element appears between two sibling elements of a common parent group, it should stay in that position after normalization — it should not be moved to the end.
- The position of each distinct group within the output should be determined by where that group first appears in the input, not by any other ordering heuristic.
- Group identity must be based on the actual individual group ID values — groups that happen to produce the same concatenated string but have different individual IDs should be treated as separate groups.

## Why This Matters

Operations such as duplicating elements or loading a saved document trigger element order normalization. If that normalization incorrectly moves subgroup elements, users will see their elements in a different layering order than they arranged them, which is unexpected and confusing. This bug causes observable z-order changes after seemingly unrelated operations.
