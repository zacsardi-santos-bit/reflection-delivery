I'm building a web application with the Litestar framework and I'd like to add Sentry monitoring to it. Right now there's no built-in Sentry integration for Litestar, so I'm not getting any automatic error capture or performance traces from my application.

I need an integration that, when initialized with my Sentry setup, will automatically capture unhandled exceptions from my route handlers and report them to Sentry with the right transaction name — either the custom route name I've assigned, or the fully-qualified Python name of the handler function. Errors should be tagged so it's clear they came through the Litestar integration.

I also need performance monitoring for middleware: each middleware in the stack should appear as a span in the transaction, labeled by the middleware's class name. If a middleware wraps the send or receive callbacks, those wrapped calls should show up as child spans as well, so I can see exactly where time is being spent in the request lifecycle.

Additionally, when I've opted into sending personally identifiable information, user data that's been set on the request scope (like email, username, and ID) should be attached to error events. When PII sending is disabled, that user data should be left out entirely.

All the spans this integration creates should carry a consistent origin label so I can filter them easily in Sentry.
