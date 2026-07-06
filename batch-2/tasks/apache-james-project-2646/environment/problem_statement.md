## Description

Mailbox rename operations do not correctly honor access control rights granted to other users. When a user has been explicitly given permission to manage a shared mailbox (such as the right to delete it or to create children under a parent mailbox), those rights are currently ignored during rename and move operations.

## Current Behavior

- Attempting to rename a shared mailbox when the user has the necessary ACL rights either fails entirely or returns a misleading error.
- When a user lacks the rights to rename a shared mailbox, the error returned is either "not found" or a generic argument-validation error — neither of which clearly communicates that the operation was rejected for insufficient permissions.

## Expected Behavior

- A user granted the right to delete a shared mailbox should be able to rename (move) it.
- A user granted the right to create child mailboxes under a shared parent should be able to rename a mailbox into that parent.
- When both rights are present (delete on source, create on destination parent), the rename must succeed.
- When rights are absent, the server must return a clear access-denied (forbidden) error rather than a misleading "not found" or generic validation error.
- The error type in mailbox update responses for unauthorized operations on delegated mailboxes must consistently signal a permission problem rather than a generic argument or not-found failure.

## Why This Matters

The current behavior makes shared mailbox management unreliable: legitimate delegated users cannot exercise permissions they have been granted, and error messages do not accurately reflect the underlying access control problem. This makes it impossible to build proper mailbox sharing workflows that include rename and reorganization operations.
