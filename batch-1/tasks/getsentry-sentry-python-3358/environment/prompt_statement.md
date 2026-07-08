I'm building a web app on the Litestar framework and there's no Sentry integration for it yet, so I'm flying blind, no automatic error capture, no performance traces, and none of the user context I got for free on other Python web frameworks. I want to write a proper Litestar integration (something like `@sentry_sdk/integrations/litestar.py`) that hooks in when I set up Sentry.

The main thing is unhandled exceptions from my route handlers should get captured and reported automatically. Each event needs the right transaction name, so if I've assigned a custom route name it uses that, otherwise it falls back to the fully-qualified Python name of the handler function. And the events should carry Litestar-specific mechanism metadata (tagged with a type like "litestar" and marked as not handled) so it's obvious in Sentry these came through this integration.

I also want performance monitoring on the middleware stack. Each middleware should show up as a span labeled by its class name, and if a middleware wraps the send or receive callbacks those wrapped calls need to appear as child spans too (with sensible op labels like the receive and send operations) so I can actually see where time goes in the request lifecycle.

Oh and PII: when I've opted into sending personally identifiable information, user data that's been set on the request scope (email, username, id, that kind of thing) should get attached to error events. When PII sending is off, leave that user data out completely, don't attach anything.

Also every span this integration creates should carry a consistent origin label (something like `auto.http.litestar`) so I can filter them easily in Sentry. This basically closes the observability gap for teams running production services on Litestar.
