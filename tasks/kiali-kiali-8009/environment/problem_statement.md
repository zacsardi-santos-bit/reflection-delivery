## Description

In Kiali's Ambient mesh support, waypoint proxies are responsible for routing traffic for services and/or workloads within the mesh. Currently, there is no way to see which services are enrolled under a specific waypoint proxy when viewing that proxy's details. Likewise, when viewing a service, there is no way to trace back to the waypoint proxy that handles its traffic. This makes it hard for operators to understand the relationships between waypoint proxies and the services they manage.

## Expected Behavior

- Given a waypoint proxy, it should be possible to retrieve the list of services whose traffic it is responsible for handling.
- Given a service, it should be possible to retrieve the waypoint proxy (or proxies) that handle its traffic.
- When fetching the details of a waypoint workload that is configured to handle service traffic (or all traffic), the response should include both the enrolled services and the enrolled workloads.
- Enrolled workloads that are not themselves waypoint proxies should reference their associated waypoint, but should not include a list of enrolled services.
- The data returned for enrolled services and waypoint workloads should include name, namespace, cluster, and label origin information.

## Why This Matters

Operators managing Ambient mesh deployments need full visibility into how waypoint proxies relate to services. Without this, diagnosing misconfigurations — such as a service not being covered by any waypoint, or two waypoints both claiming the same service — requires manual inspection of labels across multiple resources. Surfacing these relationships directly in the API makes configuration validation significantly easier.
