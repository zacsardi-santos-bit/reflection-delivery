I'm working on a Ktor server application with server-side sessions and I've run into two limitations I need to fix.

*   The cookie session builder's identity DSL function must accept a new overload that takes a lambda receiving the current request context (ApplicationCall) and returns a String. This lambda is called to generate the session ID for each new session, allowing the ID to be based on request properties such as headers.

*   When the identity lambda is registered with ApplicationCall access, the value it returns must become the session cookie value sent to the client. Subsequent requests presenting that cookie value must be able to retrieve the stored session data.

*   The identity lambda that accepts ApplicationCall must be called once per new session creation (not per request), and the session ID it produces must be stored in the session storage under that key.

*   A new suspend extension function must be added to CurrentSession that accepts a session type parameter (reified T) and a session ID string, and removes that session from server-side storage. This clears only the targeted session; all other sessions must remain intact and functional.

*   The clear-by-ID operation must work without requiring the session being cleared to be active in the current request. It must operate directly on the session storage using the provided ID string.

*   If no session provider is registered for the given type, or if the registered provider does not use server-side session IDs, the clear-by-ID operation must throw an IllegalStateException.

*   The clear-by-ID extension function must be a suspend function, since it performs asynchronous storage operations.

*   The CookieIdSessionBuilder class must also expose a provideSessionId(call: ApplicationCall): String method that dispatches to the configured identity lambda (whether it accepts ApplicationCall or not), to allow the session tracker to request a session ID with request context available.


*   Interface details: Type: Function
Name: identity
Location: ktor-server/ktor-server-plugins/ktor-server-sessions/common/src/io/ktor/server/sessions/SessionsBuilder.kt
Signature: identity(f: (ApplicationCall) -> String)
Description: New overload of the identity function in CookieIdSessionBuilder<S> that accepts a lambda receiving an ApplicationCall and returning a String. When configured, this lambda is called to generate the session ID for each newly created session. The ApplicationCall is available for deriving the ID from request context (e.g., headers). Also add the same overload to HeaderIdSessionBuilder<S>.

Type: Function
Name: clear
Location: ktor-server/ktor-server-plugins/ktor-server-sessions/common/src/io/ktor/server/sessions/SessionData.kt
Signature: suspend inline fun <reified T : Any> CurrentSession.clear(sessionId: String)
Description: New suspend inline extension function on CurrentSession that deletes a specific session from server-side storage by its session ID string, for the session type T. Must not affect other sessions or the current request's session state. Uses reified T to look up the session provider by type.

Type: Function
Name: clear
Location: ktor-server/ktor-server-plugins/ktor-server-sessions/common/src/io/ktor/server/sessions/SessionData.kt
Signature: suspend fun <T : Any> CurrentSession.clear(klass: KClass<T>, sessionId: String)
Description: Non-inline version of the clear-by-ID extension, accepting a KClass<T> and sessionId. Called by the inline reified variant above.

Type: Function
Name: clear
Location: ktor-server/ktor-server-plugins/ktor-server-sessions/common/src/io/ktor/server/sessions/SessionData.kt
Signature: suspend fun CurrentSession.clear(name: String, sessionId: String)
Description: New default suspend method added to the CurrentSession interface that clears the session identified by sessionId from the storage of the named session provider. Must throw IllegalStateException if no provider is registered for name or if the provider does not use session IDs.

Type: Function
Name: clearById
Location: ktor-server/ktor-server-plugins/ktor-server-sessions/common/src/io/ktor/server/sessions/SessionTrackerById.kt
Signature: suspend fun clearById(sessionId: String)
Description: Public suspend method on SessionTrackerById that directly invalidates the given sessionId from the underlying SessionStorage. Delegates to storage.invalidate(sessionId).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.