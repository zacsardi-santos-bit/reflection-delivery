Implement session authentication enforcement and session state tracking in the vSphere API simulator. Ensure that unauthenticated clients receive appropriate error responses and that session-specific data is correctly managed.

*   Update the `getObject` function in `simulator/property_collector.go`:
    *   Accept a `*Context` as the first parameter.
    *   Use the global registry if `ctx.Session` is nil.
    *   Use the session-scoped registry if `ctx.Session` is non-nil.

*   Modify the `collect` method on `PropertyCollector` in `simulator/property_collector.go`:
    *   Accept a `*Context` as the first parameter to propagate session state.

*   Configure the `internalContext` variable in `simulator/session_manager.go`:
    *   Ensure it is of type `*Context` with a non-nil `Session` containing a UUID `Key` and an associated `Registry`.

*   Implement session state checks:
    *   Return `nil` from `SessionManager.UserSession` if the client has no active session.
    *   Return an empty `PropSet` and a `MissingSet` with `types.NotAuthenticated` faults when retrieving properties without an active session.
    *   Return a SOAP fault with `VimFault` of type `types.NotAuthenticated` for API methods requiring authentication when the client has no active session.

*   After a client logs in:
    *   Ensure `SessionManager.UserSession` returns a non-nil session object with the `UserAgent` field set to the client's HTTP request `User-Agent`.
    *   Populate `PropSet` with requested property values and return an empty `MissingSet` when retrieving properties.
    *   Update the session's `LastActiveTime` after each authenticated API call.

*   Manage session-specific objects:
    *   Store property collectors in the session-scoped registry.
    *   Ensure their `Reference().Value` contains the session key (`session.Key`) as a substring.

*   Handle login attempts:
    *   Fail and return an error if a login attempt is made while an active session exists.
    *   Do not create a second concurrent session for the same connection.

*   Ensure `WaitForUpdates` returns a result with one entry per filter created in the current session.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.