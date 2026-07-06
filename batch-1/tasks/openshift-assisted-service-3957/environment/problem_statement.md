## Description

The image service container currently has no way to know its own externally accessible URL. It has configuration for reaching other services (like the assisted-service), but it lacks environment variables that tell it what scheme and hostname external clients use to reach it. When deployed in OpenShift, the image service is exposed via a Route resource that assigns it an external hostname, but the container has no visibility into this information.

## Expected Behavior

- The image service container should receive environment variables that describe its own externally accessible scheme and hostname.
- The scheme should always be HTTPS, reflecting the secure Route.
- The hostname should match the host configured in the image service's Route resource.
- These values should be populated automatically during reconciliation by reading from the Route.

## Why This Matters

Without knowing its own external URL, the image service cannot construct correct self-referential links for its endpoints. This is needed for scenarios where the service must generate URLs that clients can use to reach it — for example, when redirecting or advertising download endpoints. Populating these values from the Route ensures they stay in sync with the actual external address of the service.
