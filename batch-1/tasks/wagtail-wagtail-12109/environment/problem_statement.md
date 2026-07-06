## Description

Wagtail's collaborative editing indicator allows multiple editors to see each other's presence on a page or snippet. However, the current implementation has two significant gaps:

1. There is no way for an editor to signal that they are **actively making changes** (as opposed to just viewing the page). All editors appear the same in the presence list, making it impossible to distinguish between someone who left the tab open and someone who is actively typing.

2. The presence system has no awareness of **saved revisions**. If another editor saves a new version while you are editing, you only find out when something goes wrong — there is no real-time warning that the content has changed underneath you. Editors working from an outdated copy have no way to know they might be creating conflicts.

## Expected Behavior

- The editing presence ping should accept information about whether the current user is actively editing, and surface that status to other editors viewing the same content.
- The ping should accept an identifier for the revision the client currently has open. If another user has saved a newer revision since then, the response should indicate who saved it and what revision it is.
- If multiple sessions belong to the same user, they should be deduplicated in the response — showing a single entry per user with the most relevant information.
- If a newer revision was saved by a user who no longer has an active session, the response should still surface that revision with a null session reference.
- If a newer revision was saved without any associated user, the response should still surface it with an empty user field.
- The results should be sorted so that the most actionable information appears first: new revisions first, then users actively editing, then others.
- The endpoint should reject GET requests with an appropriate "method not allowed" response.
- The endpoint should return a clear error for malformed input.

## Why This Matters

Without these changes, editors can silently overwrite each other's work without any warning in the UI. This feature gives Wagtail the data layer needed to warn editors about concurrent changes and show who is actively working on the same content.
