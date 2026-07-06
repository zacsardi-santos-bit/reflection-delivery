## Description

The extension management interface lets administrators view active graph extensions, but there is no way to remove them. Administrators need the ability to delete custom extensions that are no longer needed, while built-in extensions should remain protected from accidental removal.

## Expected Behavior

- Each extension in the management interface should have a delete control.
- The delete control for built-in extensions should be visually disabled and non-interactive, since built-in extensions cannot be removed.
- Custom extensions should have an active delete control that, when clicked, opens a confirmation dialog.
- The confirmation dialog should warn the user that the action is permanent and irreversible, and require the user to confirm by typing the extension's exact name before the deletion can proceed.
- After confirming, the extension should be deleted via the backend API.
- The user should receive a success notification when the deletion completes, or an error notification if the deletion fails.
- Canceling the dialog should dismiss it without performing any deletion, and if the dialog is reopened, the confirmation input should be cleared.

## Why This Matters

Without this capability, administrators have no self-service way to clean up unwanted custom extensions from the management interface. The name-confirmation requirement ensures that permanent deletions are intentional, reducing the risk of accidental data loss.
