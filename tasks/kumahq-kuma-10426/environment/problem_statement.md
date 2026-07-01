## Description

Zone ingress proxies in a multizone setup can miss available-services updates if they are temporarily offline. Currently, the list of services that a zone ingress advertises is only computed and stored when the ingress is actively connected and requesting configuration. If an ingress goes offline and comes back later, it may hold stale information about which services are available in the zone.

## Expected Behavior

- A dedicated background component should run on a configurable interval and periodically compute the list of available services from all dataplanes and external services in the zone.
- This component should write the updated available services to every zone ingress resource in the store — not just those that are currently connected.
- An ingress that was offline when a change happened should reflect the correct state as soon as it reconnects or the store is queried.
- When all dataplanes for a service are removed, all zone ingresses (including currently offline ones) should eventually show an empty list of available services.
- The update interval should be configurable through the zone control plane configuration (both via config file and environment variable).

## Why This Matters

Without this change, a zone ingress that is temporarily offline can return outdated routing information after it comes back. In production environments with multiple ingress proxies and rolling restarts, this results in intermittent connectivity failures and incorrect service discovery until the stale proxy is updated. Decoupling the available-services computation from the individual ingress connection lifecycle ensures that all ingresses always converge on the correct state.
