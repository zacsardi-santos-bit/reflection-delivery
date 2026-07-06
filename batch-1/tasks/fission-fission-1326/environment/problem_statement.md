## Description

Fission's HTTP trigger routes support ingress configuration including host rules and annotations, but currently there is no way to configure TLS termination for these ingresses via the CLI. Users who want HTTPS on their serverless routes must manually patch the Kubernetes ingress object outside of Fission, which is error-prone and inconsistent with the rest of the ingress management workflow.

## Expected Behavior

- When creating or updating an HTTP trigger route with ingress enabled, users should be able to specify the name of a Kubernetes Secret that contains the TLS certificate and key.
- When a TLS secret is specified, the resulting Kubernetes ingress resource should be created with TLS configuration referencing that secret and the ingress host.
- When updating a route, users should be able to replace an existing TLS secret with a different one.
- Users should also be able to remove TLS configuration from an existing route by passing a designated single-character sentinel value that signals removal.
- When no TLS option is provided during an update, any existing TLS configuration should be preserved.

## Why This Matters

Without this feature, operators cannot manage HTTPS termination for Fission routes through the standard CLI, forcing them to maintain ingress TLS configuration out-of-band. This creates operational overhead and makes it harder to manage the full lifecycle of secure serverless routes in a consistent way.
