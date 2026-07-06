I'm working with a message router that supports middleware, but every middleware I add applies globally to all handlers. I'd like to be able to add middleware to a specific handler so it only runs for that one handler, while other handlers are unaffected.

Ideally, when I register a handler with the router, the registration call should return something I can chain an additional middleware call onto, without affecting any other handlers. Global middleware added to the router should still apply to all handlers as before.

The ordering behavior matters too — all middleware, whether global or handler-specific, should respect the same ordering rules as the existing global middleware: the last one registered wraps the outermost layer and runs first. This unified ordering needs to work correctly even when global and handler-specific middleware calls are interleaved during setup, and when multiple handlers each have their own middleware.
