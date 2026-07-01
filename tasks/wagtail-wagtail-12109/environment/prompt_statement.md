I'm working on improving the collaborative editing presence feature in a Wagtail project. Right now, the presence ping endpoint only shows which users are viewing the same page, but it doesn't tell me whether someone is actively making edits, and it has no awareness of whether another user has already saved a newer version of the content since I opened it.

I want to extend the ping endpoint so that:
- It only accepts POST requests (GET should be rejected with an appropriate error)
- It accepts an optional flag indicating whether the current user is actively editing
- It accepts an optional identifier for the revision the client currently has open
- It returns richer information about other users — specifically whether they are actively editing and whether they have saved a newer revision since the one the client currently has
- When multiple sessions belong to the same user, they are merged into a single entry in the response
- If a newer revision was saved by a user with no active session, the response still surfaces that revision with a null session reference
- If a newer revision was saved without any associated user, the response returns an empty string for the user field
- Results are ordered so the most important information appears first: users with new revisions, then users actively editing, then others sorted by session ID
- Malformed input returns a clear error response with HTTP 400

The model backing these sessions also needs to track the "is actively editing" flag persistently so other users can see it in subsequent pings.
