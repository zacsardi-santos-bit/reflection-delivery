## Description

When making HTTPS requests through an HTTP proxy, the current implementation only sends proxy credentials after being challenged — that is, it waits for the proxy to reject the initial tunnel-establishment request before supplying any authentication information. This reactive-only approach requires an extra network round trip for every new connection, even when the client already knows the proxy's credentials.

We should support **preemptive proxy authentication**: allowing the application's proxy authenticator to supply credentials proactively, before the proxy issues a challenge. This avoids the unnecessary back-and-forth for clients that already know what credentials to use.

## Expected Behavior

- Before sending the initial tunnel-establishment request to a proxy, the client should consult the proxy authenticator with a synthetic challenge that signals a preemptive authentication opportunity.
- If the authenticator decides to participate, it returns a request with credentials already attached; that credentialed request is used for the initial tunnel attempt.
- If the authenticator declines (returns nothing), the tunnel request goes out without credentials, and the existing reactive challenge-response flow takes over if the proxy responds with an authentication demand.
- When both preemptive and reactive authentication happen in sequence on the same connection, the authenticator is called twice — first with the preemptive synthetic challenge, then with the real proxy challenge.
- Credentials provided for tunnel establishment must not leak into subsequent requests sent to the origin server.

## Why This Matters

Clients connecting to authenticated proxies currently always pay an extra round trip per connection. Preemptive authentication lets well-configured clients skip this overhead entirely, and gives application developers a clear, documented hook for supplying proxy credentials without waiting to be asked.
