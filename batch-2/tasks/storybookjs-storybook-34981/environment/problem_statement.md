## Description

When an AI agent generates and pushes a review into the Storybook review addon, the review is cached on the dev server and replayed to every browser tab that connects later. The problem is that if a developer continues editing source files after the review was created, the review silently becomes outdated. There is currently no mechanism for the server to detect this and notify connected clients.

## Expected Behavior

- The core dev server should expose a way for any add-on to subscribe to source-file change notifications during a development session.
- The review addon should subscribe to these notifications and automatically mark the cached review as stale when a source file changes after the review was created.
- A brief tolerance window should be applied so that file-system events arriving just after a review is created do not immediately cause a false stale signal.
- Once a review is flagged as stale, that status should be preserved in the cached review so that tabs connecting later also see it.
- A single stale notification should be sufficient — multiple consecutive file changes should not produce repeated stale events.
- Pushing a fresh review from the agent should reset the stale status.

## Why This Matters

Without this feature, a reviewer can be looking at an outdated review with no indication that the underlying code has changed since it was generated, leading to confusion about whether review comments still apply to the current state of the codebase.
