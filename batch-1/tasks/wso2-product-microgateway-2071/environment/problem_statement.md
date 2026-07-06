## Description

The API gateway currently does not enforce any timeout limits when routing requests to upstream backend services. When a backend takes too long to respond, the gateway holds the connection open indefinitely. This can lead to resource exhaustion and unpredictable behavior for API consumers — there is no gateway-level guarantee that a slow or unresponsive backend will ever produce a client-visible error.

## Expected Behavior

- All routes created by the gateway should have a maximum request duration enforced at the gateway level, so that requests to slow backends are eventually terminated and callers receive an appropriate timeout error.
- Routes should also have an idle connection timeout enforced, so that connections with no active traffic are eventually closed.
- The default maximum request duration should be 60 seconds, and the default idle timeout should be 300 seconds. These should be configurable.

## Why This Matters

Without these defaults, a backend that hangs on a request causes the gateway to hold resources open until the backend responds (or the client disconnects). This makes it impossible to rely on the gateway as a protective layer against slow dependencies. Setting sensible default timeouts ensures that API consumers receive timely errors and that the gateway can reclaim resources from stuck connections.
