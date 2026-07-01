# Add notification support to the `#[rpc]` derive macro

## Description

The JSON-RPC 2.0 specification defines two types of calls: **methods** (which expect a response) and **notifications** (which do not). Currently, the `#[rpc]` derive macro only supports methods that return a result — there is no way to declare a notification handler directly in a trait definition.

This means developers who want to handle notifications must bypass the derive macro entirely or use workarounds, which breaks the ergonomic API the macro is supposed to provide.

## Expected Behavior

- A trait method with no return value (unit return type) annotated with `#[rpc(name = "...")]` should be treated as a notification handler.
- When a notification request (without an `id`) is received for such a method, the server should process it and return no response.
- When a regular method call (with an `id`) is sent to a notification-registered endpoint, the server should respond with a "Method not found" error (code `-32601`), since notification handlers should not be callable as regular RPC methods.
- Notification methods should work alongside pub/sub subscription methods in the same trait, with no conflicts.

## Why This Matters

Without this feature, the derive macro does not fully cover JSON-RPC 2.0 semantics. Developers cannot express the intent of a fire-and-forget notification handler at the trait level, making the API incomplete and forcing manual workarounds. Supporting unit-return methods as notifications makes the library a complete and ergonomic solution for JSON-RPC 2.0 servers.
