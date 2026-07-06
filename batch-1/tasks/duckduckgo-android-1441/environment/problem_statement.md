## Description

When a user saves a bookmark or edits an existing saved site, the app does not carry along the parent folder context. This means that dialogs for editing a saved site have no knowledge of which folder the bookmark lives in, making it impossible to display the correct current location or allow the user to change it within the same flow.

Additionally, when the user is selecting a folder to save a bookmark into, there is no way to create a new folder inline — the picker only shows existing folders without any option to add a new one. After creating a new folder elsewhere and returning to the picker, the list does not refresh to reflect the newly created folder.

Finally, there is no repository operation to look up a bookmark folder by its own ID, which is needed to retrieve folder details when displaying or editing a saved site.

## Expected Behavior

- Saving a bookmark or editing a saved site should pass the parent folder information along with the saved site, so the edit dialog knows which folder the bookmark currently belongs to.
- The folder picker screen should support adding a new folder inline. After a new folder is created, the folder structure list should immediately update to include it.
- The bookmarks repository should provide a way to look up a specific folder by its own ID and return the full folder details.

## Why This Matters

Without the parent folder context, users editing a bookmark cannot see or change the folder it belongs to. Without inline folder creation in the picker, users are forced to navigate away just to create a folder. These gaps make bookmark organization cumbersome and incomplete.
