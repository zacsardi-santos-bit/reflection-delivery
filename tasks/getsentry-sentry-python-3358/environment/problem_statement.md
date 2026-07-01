## Description

We need a Sentry integration for the Litestar web framework. Currently, applications built on Litestar have no automatic Sentry support — exceptions go uncaptured, there is no performance visibility into middleware execution, and user context is not included in error reports. Developers who have moved from other Python web frameworks (which already have Sentry integrations) to Litestar lose the observability they rely on.

## Expected Behavior

- Unhandled exceptions raised in route handlers should be automatically captured and reported to Sentry. Each exception event should be associated with the correct transaction name — either a custom route name if one was specified, or the Python-qualified name of the handler function.
- Exception events should carry Litestar-specific mechanism metadata so they can be identified as originating from the Litestar integration.
- Middleware execution should be tracked as performance spans, labeled by middleware class name, so developers can see where time is spent in the request pipeline.
- Calls to the receive and send callbacks within middleware should also be captured as child spans with appropriate operation labels.
- All spans produced by the integration should be tagged with a consistent origin identifier.
- When the application is configured to send personally identifiable information, user data present in the request context (such as email, username, and user ID) should be attached to exception events. When PII is disabled, user data must be omitted from events.

## Why This Matters

Teams building production services on Litestar deserve the same error monitoring and performance observability experience that Sentry provides for other Python frameworks. This integration closes that gap.
