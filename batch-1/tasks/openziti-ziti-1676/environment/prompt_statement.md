I'm trying to update our integration tests and SDK usage to work with a newer version of our networking SDK, but after the update several things have changed in ways that break our existing code.

First, the constructors for both the management API client and the client API client now require a third argument that wasn't there before — the old two-argument calls don't compile anymore. I need to update all the places where we create these clients to pass the extra argument (which can be left empty when no special configuration is needed).

Second, the way you access the session token from an authenticated session has changed. Previously it was a direct field on the session object; now you have to call a method to retrieve it. Every place we check that the token is non-empty needs to be updated to use the method call.

Third, the authentication state event system has been reworked. The callbacks we register for events like "fully authenticated", "partially authenticated", and "unauthenticated" previously received a session detail type from the generated API model; now they receive a different session type defined in the updated networking SDK. The channel types and listener registrations need to reflect this.

Fourth, there's an internal field on the controller client that holds the current API session — it used to be called one thing, and now it's called something else. Any code that accesses the current session ID through that field needs to use the new name.

Finally, I've noticed that errors from certificate-based authentication don't always surface the underlying HTTP error details. We need to wrap those errors to get a more useful diagnostic message.

On the test infrastructure side, there's also a race condition when cleaning up the test database file between test runs — sometimes the file is still held open, so the removal fails. Adding a short wait and a single retry before giving up would make the tests more reliable.
