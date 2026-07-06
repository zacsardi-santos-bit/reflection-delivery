Implement support for JSON-RPC notification handlers in the `#[rpc]` derive macro. Ensure that trait methods with a unit return type are correctly recognized and processed as notification handlers, following JSON-RPC 2.0 semantics.

*   Update the `#[rpc]` derive macro to recognize trait methods with a unit return type as notification handlers.
    *   Methods must be annotated with `#[rpc(name = "...")]`.
*   Ensure that notification handlers do not produce any response when called via a JSON-RPC request without an `id` field.
    *   The `IoHandler::handle_request_sync` method should return `None` for such requests.
*   Implement error handling for requests with an `id` field sent to a notification handler.
    *   The server must respond with a JSON-RPC error object with error code `-32601` and message `"Method not found"`.
    *   The error response format must be `{"jsonrpc":"2.0","error":{"code":-32601,"message":"Method not found"},"id":<id>}`.
*   Ensure compatibility of notification methods with pub/sub subscription methods in the same `#[rpc]`-attributed trait.
    *   Both notification and pub/sub methods must coexist without conflict.
*   Apply these changes in the files `derive/tests/macros.rs` and `derive/tests/pubsub-macros.rs`.
*   Support any trait method with a unit return type and an `#[rpc(name = "...")]` attribute as a notification handler, not limited to the example method name `notify`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.