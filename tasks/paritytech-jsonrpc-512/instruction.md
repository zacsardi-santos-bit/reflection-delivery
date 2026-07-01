Implement support for multiple subscribe methods per subscription in the `jsonrpc-derive` crate's pubsub macro system. Allow each subscription to have multiple entry points with different method names and parameters, while sharing the same unsubscribe handler. Ensure type consistency among subscribe methods and provide clear compile-time error messages for mismatches.

*   Update the `MethodRegistration::PubSub` enum variant:
    *   Store multiple subscribe methods using `Vec<RpcMethod>` in a field named `subscribes`.
    *   Ensure the `unsubscribe` field remains as `RpcMethod`.

*   Modify the derive macro to:
    *   Allow multiple `#[pubsub(subscription = "...", subscribe, name = "...")]` methods for the same subscription name, each with a unique `name`.
    *   Register each subscribe method independently, ensuring each returns a valid subscription ID.

*   Ensure all subscribe methods for the same subscription use the same `Subscriber<T>` type:
    *   If mismatched types are detected, emit a compile-time error with the message: `Inconsistent signature for 'Subscriber' argument: {actual_type}, previously defined: {first_type}`.
    *   Span the error to the argument with the mismatched type.

*   Implement the `subscriber_arg` method on `RpcMethod`:
    *   Return the `Subscriber`-typed argument as a `syn::Type`, or `None` if not found.

*   Update the `compute_method_registrations` function:
    *   Collect multiple subscribe methods per subscription into a `Vec<RpcMethod>`.
    *   Validate type consistency across subscribe methods using `subscriber_arg()`.

*   Update delegate generation code:
    *   Iterate over all subscribe methods in `subscribes` and call `add_subscription` for each, sharing the same unsubscribe handler.

*   Update client code generation:
    *   Generate a separate client method for each subscribe entry point in `subscribes`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.