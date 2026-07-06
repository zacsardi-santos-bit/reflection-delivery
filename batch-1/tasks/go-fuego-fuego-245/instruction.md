Implement the ability for a server to accept a pre-created network listener, allowing advanced use cases such as socket inheritance and zero-downtime restarts. Ensure that server startup errors are properly propagated to the caller.

*   Implement the `WithListener` function in `server.go`:
    *   Accept a `net.Listener` as a parameter.
    *   Return a function that takes a pointer to a `Server` and assigns the listener to the server's unexported `listener` field.
    *   Ensure that when `WithListener` is used, the `listener` field in the `Server` struct is non-nil.

*   Update the `Server` struct in `server.go`:
    *   Add an unexported field `listener` of type `net.Listener`.
    *   Ensure the `listener` field is nil when `NewServer` is called without `WithListener`.

*   Modify the `Run` method in `serve.go`:
    *   Use the `listener` field to serve HTTP requests if it is non-nil.
    *   Return a non-nil error if the server cannot start due to an invalid configured address, such as '----:nope'.

*   Modify the `RunTLS` method in `serve.go`:
    *   Ensure it returns a non-nil error if the server cannot start due to an invalid configured address, such as '----:nope'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.