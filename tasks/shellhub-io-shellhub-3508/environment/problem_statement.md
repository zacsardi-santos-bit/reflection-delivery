## Description

ShellHub namespace administrators currently have no way to set a custom message that appears when someone establishes a connection to a device within their namespace. It would be useful to allow each namespace to define a "connection announcement" — a short text that is automatically shown to users at the start of a session.

This requires changes across multiple layers of the system:

- The namespace settings data model must be extended to include the connection announcement field.
- Existing namespaces in the database need to be migrated so that the new field is initialized properly. The migration should be reversible.
- When a new namespace is created during initial system setup, the connection announcement must be initialized to an empty string by default.
- The namespace editing interface in the frontend must allow administrators to view and update the connection announcement alongside the namespace name. The input should appear as an editable text area, clearly labeled, and include a short description explaining its purpose.
- Saving the updated settings should send both the namespace name and the connection announcement to the backend together. If the update fails, an appropriate error notification should be displayed.
- The frontend store that manages namespace data must reflect the presence of this new field in the namespace settings structure.

## Expected Behavior

- Namespace settings include a connection announcement field that defaults to an empty string.
- A database migration (number 64) initializes the field for existing namespaces on upgrade and cleanly removes it on rollback.
- The namespace edit screen shows a labeled text area for the connection announcement, with the description: "A connection announcement is a custom message written during a session when a connection is established on a device within the namespace."
- Saving the edit form sends both the name and the connection announcement to the backend.
- If saving fails, an error notification is shown to the user.

## Why This Matters

Namespace administrators need a way to communicate important information or policies to users connecting to their devices. Without this feature, there is no built-in mechanism to display such messages automatically at connection time.
