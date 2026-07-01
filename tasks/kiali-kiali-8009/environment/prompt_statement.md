I'm working on Kiali's Ambient mesh support and I need to add bidirectional visibility between waypoint proxies and the services they manage. Right now, when I look at a waypoint workload's details, I can see the workloads it covers, but there's no information about which services are enrolled under it. Similarly, when looking at a service, I can't tell which waypoint proxy is responsible for its traffic.

I need two new capabilities in the service business layer: one that, given a waypoint's name and namespace, returns all the services enrolled under that waypoint; and another that, given a service, returns the waypoint workloads handling that service's traffic.

I also need to update the workload model so that when a waypoint workload is fetched, the response includes both the enrolled workloads and the enrolled services (when the waypoint is configured to handle service or all traffic). For enrolled non-waypoint workloads, the response should reference the waypoint by name but should not include enrolled services.

The data structures for enrolled services and enrolled waypoints should carry the name, namespace, cluster, and a field indicating where the enrollment label comes from (for example, whether it was set on the service directly, on a namespace, or on a workload). The existing field that stores enrolled workloads on the waypoint should also be updated to use this lighter-weight reference type instead of the full workload model.

I also need a test helper that sets up fake Kubernetes clients containing a waypoint deployment, an enrolled service (labeled with the waypoint enrollment label), and an unenrolled service, so these behaviors can be tested end-to-end.
