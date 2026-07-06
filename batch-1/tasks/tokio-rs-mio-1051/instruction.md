Implement thread-safety for the `Registry` and `Poll` types and provide a mechanism for creating independent copies of a `Registry`. Ensure these types can be shared across threads using standard shared-pointer containers and that they support concurrent operations.

*   Implement the `Send` and `Sync` traits for the `Registry` type in `src/poll.rs`.
    *   This allows `Registry` to be safely moved to or shared across threads.
*   Implement the `Send` and `Sync` traits for the `Poll` type in `src/poll.rs`.
*   Implement a `try_clone` method for the `Registry` type with the signature `try_clone(&self) -> io::Result<Registry>`.
    *   This method should create a new independently owned `Registry` that references the same underlying selector.
    *   Ensure the method returns an error if the OS-level duplication fails.
*   Ensure that when a `Registry` is cloned via `try_clone` and used from a separate thread to register I/O sources (e.g., `TcpListener`, `TcpStream`), the original `Poll` instance can poll for events on the main thread, producing at least one readiness event.
*   Ensure the `registry()` method on `Poll` returns a reference `&Registry`.
    *   This allows callers to invoke `register`, `reregister`, `deregister`, and `try_clone` directly without storing an intermediate value.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.