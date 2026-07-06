## Description

When an ingress controller is deployed behind a load balancer or reverse proxy that sets a forwarding header to indicate the original protocol (e.g., marking traffic as already coming from HTTPS), redirect rules intended to enforce HTTPS can cause infinite redirect loops.

The problem occurs because redirect routes are generated unconditionally: when a route is configured to redirect from HTTP to HTTPS, the redirect fires regardless of whether the forwarding header already indicates that the request was received over HTTPS. A client hitting the load balancer over HTTPS gets forwarded to the ingress with the header already set to "https", but the ingress still issues a redirect response — causing the browser to loop.

## Expected Behavior

- When generating an HTTPS redirect route, the route's match conditions should include a check of the forwarding header that inverts the match on the target scheme. The redirect should only be triggered when the forwarding header does **not** already indicate that the connection is using the target protocol.
- Backend routes (routes that forward traffic to services) should not be affected by this change — no forwarding header check should be added to them.

## Why This Matters

This change prevents infinite redirect loops for users who deploy behind TLS-terminating proxies or load balancers that set forwarding headers. Without this fix, any site enforcing HTTPS via redirects may become unreachable when placed behind such infrastructure.
