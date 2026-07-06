Implement the necessary changes to ensure Istio's control plane correctly handles route discovery subscriptions for Envoy proxies. Ensure that route subscriptions are respected during global pushes, and that the nonce-based acknowledgment protocol is correctly managed.

*   Implement route subscription tracking per connection:
    *   Update the connection's route subscription upon receiving a fresh route discovery request with an empty acknowledgment token.
    *   Treat requests with non-matching acknowledgment tokens as stale and ignore them without updating subscriptions or triggering pushes.
    *   Handle requests with matching acknowledgment tokens:
        *   If the version info and route names match the current subscription, treat as an ACK with no re-push.
        *   If route names are nil or empty, log the protocol error, maintain the current subscription, and do not push.
        *   If route names differ from the current subscription, update the subscription and push only the newly subscribed routes.

*   Ensure correct behavior during global pushes:
    *   Deliver only the routes that each connection most recently subscribed to.
    *   If a connection subscribed to a single route, ensure a global push returns only that route.

*   Manage nonce-based acknowledgment protocol:
    *   Track the last version info sent in route responses to distinguish between ACKs, NACKs, and protocol errors.
    *   Ensure subsequent fresh requests are served normally after a protocol error is logged.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.