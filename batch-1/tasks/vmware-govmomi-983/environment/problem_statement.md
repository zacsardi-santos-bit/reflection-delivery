## Description

The vSphere API simulator in this library does not enforce session authentication. A client that connects to the simulator without logging in can call any API method and receive a successful response, which is inconsistent with how a real vSphere server behaves. Additionally, the simulator does not properly track session metadata: the user agent and last-active timestamp are not recorded or updated during a session, and per-session objects such as property collectors are not scoped to their owning session — their identifiers carry no session-specific information.

## Expected Behavior

- Unauthenticated clients should receive authentication error responses when calling protected API methods.
- When an unauthenticated client retrieves object properties, the result should reflect the lack of authentication (empty property set with authentication fault entries) rather than returning actual property values.
- A client that has not logged in should see no active session when querying session state.
- After a successful login, the session should record the client's user agent and should update the last-active timestamp as the client makes further API calls.
- Objects created within a session (such as property collectors) should be scoped to that session, and their identifiers should reflect session ownership.
- Attempting to log in a second time without first logging out should be rejected.
- After logging out and logging back in, the session should be fully functional again, including creating property collector filters and receiving update notifications.

## Why This Matters

Without proper authentication enforcement, the simulator cannot be used to write realistic integration tests that verify how client code behaves when it lacks credentials or when its session expires. The simulator should faithfully reproduce the authentication and session lifecycle semantics of a real vSphere server.
