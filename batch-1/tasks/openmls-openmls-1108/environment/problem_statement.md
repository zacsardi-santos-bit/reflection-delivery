## Description

The current validation rule for whether a commit requires an update path is too narrow. It only checks for Remove and Update proposals, but there are other proposal types (such as group context extension changes) that also require a path for security reasons. Conversely, some proposal types (such as adding members or pre-shared key injections) do not require a path at all, but the current rule may incorrectly require one in certain combinations.

## Expected Behavior

- Each proposal type should explicitly declare whether it requires an update path in a commit.
- A commit should require a path if and only if at least one of its proposals requires a path (or the commit is empty, or it is an external commit).
- Add proposals and pre-shared key proposals should NOT require a path.
- Update proposals, Remove proposals, and group context extension proposals SHOULD require a path.
- When a commit contains only proposals that do not require a path, generating the commit should produce a commit without a path, and receiving such a commit without a path should succeed.
- When a commit contains at least one proposal that requires a path, receiving it without a path should be rejected with the appropriate error.

## Why This Matters

This matters for correctness and completeness of the MLS protocol implementation. Commits that only add members or inject pre-shared keys should not require a path, but currently they might be treated as requiring one depending on context. At the same time, commits that change group context extensions must require a path, but this was previously not enforced. Fixing this ensures the implementation correctly follows the MLS specification for all proposal type combinations.
