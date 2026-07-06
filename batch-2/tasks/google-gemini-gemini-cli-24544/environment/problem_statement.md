## Description

The memory manager can automatically extract reusable skills from past conversations during background processing. However, there is currently no way for users to review these auto-extracted skills before they become active — skills extracted by the memory manager are placed in a temporary location with no workflow for the user to accept, install, or discard them. Users have no visibility into what was extracted or control over where skills end up.

## Expected Behavior

- A new "inbox" concept should hold auto-extracted skills in a staging area until the user reviews them.
- Users should be able to list the skills waiting in the inbox, including each skill's name, description, and when it was extracted.
- Users should be able to install an inbox skill to either their global skills directory or their project-local skills directory.
- Users should be able to dismiss (permanently discard) an inbox skill they don't want.
- When the current workspace has not been marked as trusted, installing a skill to the project should be blocked and explained to the user.
- If a skill with the same name already exists in the destination, the move should be rejected with a clear message rather than overwriting.
- When new skills are extracted during background processing, the tool should notify the user that items are waiting for review and direct them to the inbox.
- A slash command should expose the inbox as an interactive dialog, with appropriate error messages if the experimental memory manager feature is not enabled or if the configuration has not yet loaded.
- Error conditions (failed installs, failed reloads after a successful install) should be reported inline in the dialog.

## Why This Matters

Without this feature, auto-extracted skills silently appear in the system with no user review step. Users need a way to curate what skills are actually installed, choose the right scope (global vs. project), and be notified when new skills are ready for their attention.
