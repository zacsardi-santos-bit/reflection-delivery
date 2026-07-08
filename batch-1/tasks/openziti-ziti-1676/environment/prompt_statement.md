We just bumped our networking SDK to a newer release and it broke a bunch of stuff across our test suite and integration layer. I need help getting everything compiling and passing against the new API surface. The reason we're doing this at all is that keeping the dependency current unblocks new auth mechanisms like OIDC-based token refresh, which actually needs that new config param on the client constructors, so bear with me.

Here's what changed. The constructors for both the management API client and the client API client used to take two args (a URL and a cert pool), and now they want a third argument for special config. The old two-arg calls don't compile anymore, so I need every place we build these clients updated to pass the extra arg, which can just be left empty/unset when there's no special config needed.

Also the session token access moved. It used to be a direct field on the authenticated session object, now you call a method to get it. Everywhere we check the token is non-empty needs to switch to the method call, and it should still come back non-empty after a successful login.

Then the auth state event system got reworked. The callbacks we register for "fully authenticated", "partially authenticated", and "unauthenticated" used to receive a session-detail type generated from the API spec, and now they hand you a dedicated session type defined in the updated SDK. The channel types and listener registrations need to reflect the new type, and those events should still fire correctly and deliver the new session to listeners.

Oh and there's an internal field on the controller client that stores and atomically accesses the current API session, it got renamed, so any code reading the old field name (like pulling the current session ID through it) needs the new name or it won't compile.

One more real bug: errors from certificate-based authentication don't always surface the underlying HTTP error details, so I want those wrapped to get a richer diagnostic message out of the HTTP response. And all three existing auth flows (username/password, cert, and JWT) need to keep working through all this.

Last thing, unrelated to the SDK, on the test infra side there's a race when we clean up the test database file between runs, sometimes it's still held open and the removal fails. Can you add a short wait and a single retry before giving up so the tests stop flaking.
