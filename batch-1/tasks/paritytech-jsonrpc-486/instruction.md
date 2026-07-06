Implement support for notification methods in the `#[rpc]` derive macro and the client transport layer. Ensure that the generated client code for notification methods sends notifications properly without panicking and returns a future that resolves when the notification is dispatched.

*   Update the `#[rpc]` derive macro:
    *   Recognize trait methods with `#[rpc(name = "...")]` that return unit as notification methods.
    *   Generate client methods for notifications that return `impl Future<Item = (), Error = RpcError>`.
    *   Ensure the generated client method calls `self.inner.notify(rpc_name, args_tuple)`.

*   Implement the `TypedClient::notify` method:
    *   Accept a method name string and a serializable argument tuple.
    *   Serialize the arguments to JSON and convert them to `Params`.
    *   Delegate the call to `RawClient::notify`.
    *   Return `impl Future<Item = (), Error = RpcError>`.

*   Implement the `RawClient::notify` method:
    *   Accept a method name string and `Params`.
    *   Send a JSON-RPC notification message without expecting a response.
    *   Return `impl Future<Item = (), Error = RpcError>`.

*   Ensure the notification is transmitted as a JSON-RPC 2.0 notification:
    *   Create a `NotifyMessage` with fields `method: String` and `params: Params`.
    *   Convert `NotifyMessage` to `RpcMessage` using `From<NotifyMessage> for RpcMessage`.
    *   Handle `RpcMessage::Notify(NotifyMessage)` in all transport implementations.

*   Implement the `notification` method in `RequestBuilder`:
    *   Serialize a `NotifyMessage` into a JSON-RPC 2.0 notification string.
    *   Use `jsonrpc_core::Notification` with `jsonrpc: Some(Version::V2)`.

*   Ensure a client-server round trip where `add(3, 4)` returns 7 and is followed by `notify(7)` completes successfully, with the notify future resolving to `()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.