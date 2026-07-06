## Description

Currently, the `jsonrpc-derive` pubsub macro only allows a single subscribe method per subscription. If a developer tries to define two subscribe entry points for the same subscription topic — for example, to allow subscribing via different parameter sets or to provide multiple API entry points — the macro rejects it with a compile error saying the subscribe method is already defined.

This is too restrictive. There are valid use cases where developers want multiple ways to subscribe to the same logical subscription topic, each with different parameter signatures, while sharing a single unsubscribe method. The macro should be extended to support this pattern.

## Expected Behavior

- A subscription can have more than one subscribe method, each with a unique RPC method name.
- Each subscribe method is independently registered and callable, returning a valid subscription ID when invoked.
- If multiple subscribe methods are defined for the same subscription but they use different inner subscriber types (i.e., conflicting generic type parameters on the `Subscriber`), the macro must catch this inconsistency at compile time and emit a clear error indicating which argument is inconsistent and where the original type was defined.

## Why This Matters

This allows API designers to offer multiple subscription entry points for the same topic — useful for versioned APIs, optional parameters, or different client capabilities — without having to create separate subscriptions or work around the macro's single-subscribe restriction.
