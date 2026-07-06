## Description

In our course messaging system, instructors can pin important messages to highlight them for students. However, while pinned status is tracked and displayed, there is currently no way for users to filter a conversation to view only its pinned messages. Users who want to quickly review pinned content must scroll through an entire conversation manually.

## Expected Behavior

- Each conversation header should show a count of pinned messages when one or more exist.
- Users should be able to toggle a "show pinned only" mode to filter the conversation view to display only pinned messages.
- When toggling back, all messages should be displayed again.
- The pinned message count should update automatically in real time: when a message is pinned, unpinned, or deleted, the count and filtered view should reflect the change immediately without a page reload.
- If all pinned messages are removed (count drops to zero), the filter mode should automatically turn off so the full conversation is shown.
- The server should support querying for only pinned messages in a given conversation, enabling efficient retrieval without loading all messages.

## Why This Matters

Students and instructors often rely on pinned messages to track key announcements, deadlines, or important information. Without a way to view only pinned messages, pinning becomes less useful in active conversations with many messages. This feature makes pinned messages much more accessible.
