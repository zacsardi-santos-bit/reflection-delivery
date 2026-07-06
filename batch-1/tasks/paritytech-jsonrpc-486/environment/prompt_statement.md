I'm using the `#[rpc]` derive macro to generate both a server and a client from a single trait definition. My trait has some methods that return a `Result` (regular RPC calls) and some that return nothing at all (notifications — fire-and-forget messages that don't expect a response).

The problem is that the generated client code for notification methods doesn't work. Calling a notification method on the generated client panics at runtime instead of actually sending the notification to the server. Notifications should return a future that resolves when the message has been sent, so I can chain on them just like regular calls.

The client transport layer also needs a way to send notifications — messages that are dispatched to the server without waiting for a response. Could you implement proper support for notification methods in both the derive macro's client code generation and the underlying client transport?
