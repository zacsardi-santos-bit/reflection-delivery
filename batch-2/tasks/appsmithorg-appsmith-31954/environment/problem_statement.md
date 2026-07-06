## Description

Two related utility pieces are missing from the client-side codebase that are needed for proper IDE navigation and custom widget builder window management.

**1. Post-deletion navigation in the IDE**

When a user deletes an entity (such as a query or API) that they are currently viewing, the IDE has no clear logic for deciding where to navigate next. The desired behavior is:

- If the deleted entity was not the one being viewed, stay on the current page (no redirect).
- If the deleted entity was the active one and nothing is left, take the user to a "create new" flow.
- If the deleted entity was the active one and other items remain in the same group/category, navigate to the first item in that group.
- If the deleted entity was the active one but no items in the same group remain (though other items from different groups do exist), navigate to the first available item overall.

**2. Custom widget builder window service**

There is no service to manage the lifecycle of custom widget builder popup windows. Each widget can have its own associated builder window that opens in a separate browser tab/popup. The application needs to be able to:

- Open and track a builder window per widget
- Send messages to and receive typed messages from the builder window
- Check whether a builder window is still open
- Bring a specific builder window into focus
- Cleanly close a builder window and remove all associated event listeners

## Expected Behavior

Both utilities should be implemented so that the IDE navigation after deletion is predictable and the custom widget builder workflow can properly manage popup window communication.

## Why This Matters

Without clear post-deletion navigation, users may be left on a broken/empty page after deleting an entity. Without the builder window service, custom widget development cannot properly open, communicate with, and close the builder interface.
