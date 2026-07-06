Implement enhancements to the collaborative editing presence feature in a Wagtail project. Extend the ping endpoint to handle additional data and improve response information about editing sessions and revisions.

*   Update the `EditingSession` model:
    *   Add an `is_editing` BooleanField with a default value of False to track if a user is actively making changes.

*   Modify the `PingView` endpoint:
    *   Location: `wagtail/admin/views/editing_sessions.py`.
    *   Accept only POST requests; return HTTP 405 for GET requests.
    *   Accept optional POST parameters:
        *   `is_editing` (boolean-coercible string, e.g., "1" for true).
        *   `revision_id` (integer).
    *   Update the `EditingSession` record for the current session with the `is_editing` flag if provided.
    *   Return a JSON response with:
        *   `session_id`: the current session's ID.
        *   `other_sessions`: a list of other active sessions with fields:
            *   `session_id` (integer or null).
            *   `user` (full name string, or empty string if no user).
            *   `last_seen_at` (ISO 8601 timestamp string).
            *   `is_editing` (boolean).
            *   `revision_id` (integer or null).

*   Handle revisions and sessions:
    *   If a `revision_id` is provided, identify the most recent revision created after it and include its ID in the response.
    *   Merge multiple active sessions for the same user into a single entry using the most recent session's data.
    *   If any merged session has `is_editing` set to true, reflect this in the merged entry.
    *   Create a synthetic entry for newer revisions saved by users with no active session, setting `session_id` to null and `last_seen_at` to the revision's creation timestamp.
    *   For revisions with no associated user, set `session_id` to null and `user` to an empty string.

*   Sort the `other_sessions` list:
    *   Entries with a non-null `revision_id` first.
    *   Entries with `is_editing` set to true and `revision_id` null.
    *   Remaining entries sorted by `session_id` in ascending order.

*   Error handling:
    *   Return HTTP 400 with `{"error": "Invalid data"}` for malformed input.
    *   Ensure no modification of session records occurs on error responses.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.