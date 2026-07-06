Implement a Sentry integration for the Litestar web framework to capture unhandled exceptions and monitor middleware performance. Ensure that exceptions are reported with the correct transaction names and include user data when configured. Track middleware execution as performance spans.

*   Create a class `LitestarIntegration` in `sentry_sdk/integrations/litestar.py`.
    *   Subclass `Integration`.
    *   Set `identifier = "litestar"` and `origin = "auto.http.litestar"`.
    *   Implement a static method `setup_once()` to patch Litestar internals for exception capture, middleware span instrumentation, and user data collection.
*   Capture exceptions in Litestar route handlers:
    *   Use `exception_handler(exc: Exception, scope: LitestarScope) -> None` to capture exceptions as Sentry events.
    *   Set the exception mechanism type to "litestar".
    *   Use the custom route name or the Python qualified name of the handler function for the transaction name.
    *   Include user data in the event when `send_default_pii` is True.
    *   Exclude user data when `send_default_pii` is False.
*   Track middleware performance:
    *   Create spans for each middleware with operation "middleware.litestar" and description as the middleware's class name.
    *   Tag spans with "litestar.middleware_name" set to the middleware's class name.
    *   For wrapped send callbacks, create spans with operation "middleware.litestar.send" and description as the qualified name of the send function.
    *   For receive callbacks, create spans with operation "middleware.litestar.receive", description as the qualified name of the receive callable, and tag with "litestar.middleware_name".
    *   Ensure all spans and transaction trace contexts have origin "auto.http.litestar".
*   Implement `SentryLitestarASGIMiddleware` in `sentry_sdk/integrations/litestar.py`.
    *   Subclass `SentryAsgiMiddleware`.
    *   Initialize the parent with `span_origin="auto.http.litestar"`.
    *   Ensure the internal send wrapper is named `_sentry_wrapped_send` and appears correctly in span descriptions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.