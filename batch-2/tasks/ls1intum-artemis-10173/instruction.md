Implement a feature in the Artemis course messaging system to allow users to filter conversations to display only pinned messages. Enhance the system to show a count of pinned messages in the conversation header and ensure real-time updates of pinned message status.

Requirements:

*   Update `MetisService` in `src/main/webapp/app/shared/metis/metis.service.ts`:
    *   Maintain a private `pinnedPosts$` as `BehaviorSubject<Post[]>`, initialized to an empty array.
    *   Implement `getPinnedPosts(): Observable<Post[]>` to return `pinnedPosts$.asObservable()`.
    *   Implement `fetchAllPinnedPosts(conversationId: number): Observable<Post[]>` to retrieve pinned posts using `postService.getPosts` with `pinnedOnly: true`, update `pinnedPosts$`, and return the list.
    *   Modify `handleNewOrUpdatedMessage(postDTO: MetisPostDTO)` to update `pinnedPosts$`:
        *   For `UPDATE` actions, add or update posts with `PINNED` priority in `pinnedPosts$`.
        *   Remove posts with non-`PINNED` priority using `removeFromPinnedPosts`.
        *   For `DELETE` actions, remove the post from `pinnedPosts$` if present.
    *   Implement `removeFromPinnedPosts(postId: number): void` to remove a post by ID from `pinnedPosts$`.

*   Update `ConversationMessagesComponent` in `src/main/webapp/app/overview/course-conversations/layout/conversation-messages/conversation-messages.component.ts`:
    *   Declare `allPosts: Post[]` and `pinnedPosts: Post[]` as public properties.
    *   Declare `showOnlyPinned` as an Angular input signal and `pinnedCount` as an Angular output.
    *   Implement `setPosts(): void` to call `applyPinnedMessageFilter()` before reversing and grouping posts.
    *   Implement `applyPinnedMessageFilter(): void` to set `posts` to `pinnedPosts` if `showOnlyPinned()` is true, otherwise set to `allPosts`.
    *   Modify `ngOnChanges(changes: SimpleChanges): void` to call `setPosts()` when `showOnlyPinned` changes, except on the first change.
    *   In `ngOnInit()`, subscribe to `getPinnedPosts()`, update `pinnedPosts`, and emit `pinnedCount` on changes. On active conversation change, call `fetchAllPinnedPosts()`, update `pinnedPosts`, and emit `pinnedCount`.

*   Update `ConversationHeaderComponent` in `src/main/webapp/app/overview/course-conversations/layout/conversation-header/conversation-header.component.ts`:
    *   Initialize `showPinnedMessages: boolean` to `false`.
    *   Declare `pinnedMessageCount` as an Angular input signal and `togglePinnedMessage` as an Angular output.
    *   Implement `togglePinnedMessages(): void` to toggle `showPinnedMessages` and emit `togglePinnedMessage`.
    *   Modify `ngOnChanges(changes: SimpleChanges): void` to set `showPinnedMessages` to `false` when `pinnedMessageCount` changes to 0 and it is not the first change.

*   Update `CourseConversationsComponent` in `src/main/webapp/app/overview/course-conversations/course-conversations.component.ts`:
    *   Initialize `showOnlyPinned: boolean` to `false` and `pinnedCount: number` to `0`.
    *   Implement `togglePinnedView(): void` to toggle `showOnlyPinned`.
    *   Implement `onPinnedCountChanged(newCount: number): void` to update `pinnedCount` and set `showOnlyPinned` to `false` if `newCount` is 0.

*   Modify the `GET /api/courses/{courseId}/messages` endpoint to accept a `pinnedOnly` query parameter:
    *   When `pinnedOnly=true`, return only posts with `PINNED` display priority.
    *   When `pinnedOnly=false` or omitted, return all posts matching other criteria.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.