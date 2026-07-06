## Description

The Kubernetes API server's admission webhook client currently forces all webhook connections to use HTTP/1, regardless of where the webhook endpoint lives. This was originally done to ensure TCP-level load balancing worked correctly — HTTP/2 multiplexes multiple requests over a single connection, which would prevent concurrent requests from being distributed across different backend pods.

However, this blanket restriction is overly broad. Webhooks that target loopback addresses (such as those running on the same node or in the same process) have no need for multi-backend load balancing. For these local endpoints, HTTP/2 would be strictly beneficial: it reduces connection overhead and allows concurrent requests to share a single connection without any of the load-balancing concerns that apply to external or cluster-internal endpoints.

## Expected Behavior

- Webhook connections targeting loopback addresses (localhost, 127.0.0.1, ::1, and similar) should be allowed to use HTTP/2. The transport configuration for these webhooks should not explicitly exclude HTTP/2.
- Webhook connections targeting non-loopback addresses (external hostnames, cluster service addresses, etc.) should continue to be forced to use HTTP/1, preserving the existing load-balancing behavior.
- The distinction must be applied when the webhook client configuration is built from a direct URL. The behavior for service-reference-based webhooks (which use cluster-internal service DNS names) should remain as HTTP/1 forced.

## Why This Matters

Allowing HTTP/2 for loopback webhook endpoints enables more efficient communication for locally-running webhooks, reduces per-request connection setup overhead, and better utilizes the capabilities of the underlying transport — without sacrificing the load-balancing properties needed for production, externally-addressed webhook deployments.
