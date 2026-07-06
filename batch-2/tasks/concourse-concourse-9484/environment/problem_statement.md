## Description

Concourse has a "wall" feature that lets an operator broadcast an important message to all users, shown as a banner across the top of the UI. However, the dashboard currently has no controls for administrators to actually compose, submit, or remove that message — the admin-facing management interface is completely absent.

## Expected Behavior

- A management button should appear in the dashboard top bar **only** for users who are both logged in and have admin privileges. It should not be visible to unauthenticated users or to logged-in non-admin users.
- Clicking the management button should open an inline editor panel that allows the admin to type a broadcast message and submit it, or clear any existing broadcast.
- The editor panel should close automatically when the admin navigates away from the dashboard, logs out, or successfully saves the message.
- If the save operation fails on the backend, the editor should remain open so the admin can retry.
- Clicking a "clear" control should immediately trigger removal of the existing broadcast and close the editor.
- After a successful save, the application should refresh the displayed wall message so the new content appears.
- After the broadcast is successfully cleared, the wall banner should disappear from the UI.

## Why This Matters

Without these controls, admins have no way to manage the wall message from within the Concourse UI, making the wall feature effectively one-directional. Adding the editor makes the feature fully usable for operators who want to communicate real-time status to their users.
