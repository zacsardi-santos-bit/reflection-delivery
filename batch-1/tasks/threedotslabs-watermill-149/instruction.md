Implement the ability to add middleware to a specific handler in a message router, allowing it to run only for that handler while maintaining existing global middleware functionality. Ensure the middleware ordering rule is preserved, applying middleware in the reverse order of registration.

Requirements:

*   Update the `AddHandler` method in `message/router.go`:
    *   Change the signature to return a `*Handler`:
        ```go
        AddHandler(handlerName string, subscribeTopic string, subscriber Subscriber, publishTopic string, publisher Publisher, handlerFunc HandlerFunc) *Handler
        ```
    *   Ensure backward compatibility so existing calls that ignore the return value still compile.

*   Define a new public type `Handler` in `message/router.go`:
    *   The `Handler` struct must hold references to the router and the registered handler.

*   Implement the `AddMiddleware` method on `Handler` in `message/router.go`:
    *   Signature: `AddMiddleware(m ...HandlerMiddleware)`
    *   Register middleware that applies exclusively to the specific handler.

*   Ensure router-level middleware added via `Router.AddMiddleware`:
    *   Applies to every handler registered with the router.

*   Ensure handler-level middleware added via `Handler.AddMiddleware`:
    *   Applies only to the specific handler it was registered on.
    *   Does not affect other handlers.

*   Store all middleware in a single shared registration list:
    *   Maintain the order of `AddMiddleware` calls.
    *   Skip handler-level entries for other handlers when applying middleware at runtime.

*   Apply middleware in reverse registration order:
    *   Last-registered middleware wraps outermost and is applied first.
    *   This rule applies uniformly across both router-level and handler-level middleware.

*   For N handlers sharing router-level middleware:
    *   Each piece of router-level middleware is applied once per handler.
    *   Handler-level middleware is applied exactly once, only for its associated handler.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.