## Description

When restoring or cloning block storage volumes across regions, the system needs to determine which availability zones to use in the target region. Currently, the zone resolution logic creates its own Kubernetes client internally, which makes it hard to test, creates coupling between zone selection and cluster connectivity, and prevents callers from reusing an existing client.

Additionally, the utility function that filters candidate zones against a provider's valid zone list is private, so storage provider packages cannot call it directly and instead duplicate the logic or rely on a higher-level function with more complex behavior than they need.

## Expected Behavior

- The zone resolution function should accept a Kubernetes client as a parameter rather than creating one internally. Callers (such as the AWS EBS and GCP persistent disk providers) are responsible for creating and passing the client, allowing them to handle connection failures gracefully before calling zone resolution.
- The zone filtering utility should be made publicly accessible so that storage providers can call it directly when they already have a list of available zones and simply need to validate candidates against it.
- When no valid availability zones can be found for a region, the function should return a descriptive error.
- When a region has no availability zones at all according to the provider's mapping, the function should return a distinct error.

## Why This Matters

Tight internal client creation makes the zone resolution logic difficult to unit test and forces providers to accept all-or-nothing behavior when Kubernetes is unavailable. Exposing the client as a parameter and the filtering utility as a public function allows each provider to handle partial failures (e.g., a missing Kubernetes connection) without losing the ability to fall back to statically-known zone mappings.
