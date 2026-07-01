## Description

The file management API endpoints currently lack proper permission enforcement for write operations. Any authenticated user — regardless of their workspace role — can upload new content to or delete workspace files that belong to non-conversation contexts (such as folder documents). This means a regular user with minimal permissions can inadvertently or maliciously overwrite or remove workspace content that should be protected.

## Expected Behavior

- When a non-builder user attempts to upload or replace a file that is NOT a conversation attachment, the API should reject the request with a permission error (403).
- When a non-builder user attempts to delete a file that is NOT a conversation attachment, the API should reject the request with a permission error (403).
- Conversation-related files should remain fully accessible to any authenticated user (no builder restriction), preserving existing chat functionality.
- Users with builder or admin privileges should be allowed to upload or delete any file regardless of its purpose.
- The permission check should apply consistently to both the public (API key–authenticated) and private (session-authenticated) endpoints for file operations.
- The public API should also reject non-system keys outright for write operations on non-conversation files before even reaching the builder permission check.

## Why This Matters

Without this gate, low-privilege users have unrestricted write access to workspace-managed documents, undermining role-based access control for file storage. Adding this check ensures that only trusted workspace members can mutate sensitive workspace content, while ordinary users can still manage files that arise naturally from conversations.
