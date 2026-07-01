Implement a reusable content preparation utility to handle transformations such as removing member-only content, escaping HTML, converting line breaks, and wrapping content in paragraph elements. Extend the publishing service to support publishing standalone short-form notes and ensure all publishing operations return structured results.

*   Create a `ContentPreparer` class in `src/publishing/content.ts` with a `prepare(content, options)` method.
    *   Ensure `prepare` returns the content unchanged if no options are provided.
    *   Implement transformations based on options:
        *   `removeMemberContent`: Strip content after `MEMBER_CONTENT_MARKER`.
        *   `escapeHtml`: Convert '<' to '&lt;', '>' to '&gt;', and '/' to '&#x2F;'.
        *   `convertLineBreaks`: Replace '\n' with '<br />'.
        *   `wrapInParagraph`: Enclose content in '<p>...</p>' tags.
*   Export `MEMBER_CONTENT_MARKER` as a constant from `src/publishing/content.ts`.
*   Define `PublishStatus` as an enum in `src/publishing/service.ts` with `Published` and `NotPublished` values.
*   Update `FedifyPublishingService` in `src/publishing/service.ts`:
    *   Modify the constructor to accept a `ContentPreparer` instance as the third argument.
    *   Implement `publishPost` to return a result object with `status` and `activityJsonLd` fields.
        *   Use `ContentPreparer.prepare` with `{ removeMemberContent: true }` for Members visibility posts.
        *   Return `NotPublished` status if there is no public content before `MEMBER_CONTENT_MARKER`.
    *   Implement `publishNote(note: Note, outbox)` method:
        *   Throw an error if the actor cannot be resolved for the note's author handle.
        *   On success, store two ActivityPub objects, add the Create activity to the outbox, send it to followers, and return `Published` status.
*   Define a `Note` type in `src/publishing/types.ts` with `content` and `author` fields.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.