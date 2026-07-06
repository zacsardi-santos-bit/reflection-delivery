Implement bidirectional visibility between waypoint proxies and the services they manage in Kiali's Ambient mesh support. Add methods to retrieve services managed by a waypoint and to identify waypoint proxies managing a service. Update the workload model to reflect these relationships.

Requirements:

*   Implement the `ListWaypointServices` method in `business/services.go`:
    *   Accept parameters: `ctx context.Context`, `name string`, `namespace string`, `cluster string`.
    *   Return a list of `ServiceReferenceInfo` objects.
    *   Ensure each object includes `Name`, `Namespace`, `Cluster`, and `LabelType` fields.
    *   Set `LabelType` to 'service' for services directly labeled with the waypoint label.

*   Implement the `GetWaypointsForService` method in `business/services.go`:
    *   Accept parameters: `ctx context.Context`, `svc *models.Service`.
    *   Return a list of `WorkloadReferenceInfo` objects.
    *   Ensure `LabelType` is not set (empty) for waypoints found via a service-level label.

*   Define the `WorkloadReferenceInfo` struct in `models/workload.go`:
    *   Include fields: `Cluster`, `Labels`, `LabelType`, `Name`, `Namespace`, `Type`.

*   Define the `ServiceReferenceInfo` struct in `models/service.go`:
    *   Include fields: `Cluster`, `LabelType`, `Name`, `Namespace`.

*   Update the `Workload` struct in `models/workload.go`:
    *   Add `WaypointServices` field of type `[]ServiceReferenceInfo`.
    *   Ensure this field is populated when the workload is a waypoint handling service or all traffic.

*   Change the `WaypointWorkloads` field on the `Workload` struct in `models/workload.go`:
    *   Update type to `[]WorkloadReferenceInfo`.
    *   Ensure each element includes `Name`, `Namespace`, `Cluster`, and `LabelType`.

*   Ensure when fetching a waypoint workload via `GetWorkload`:
    *   Populate `WaypointServices` with `ServiceReferenceInfo` entries.
    *   Set `LabelType` to 'service' for services enrolled via a service-level label.

*   Ensure when fetching a non-waypoint enrolled workload via `GetWorkload`:
    *   Set `WaypointServices` to nil.
    *   Populate `WaypointWorkloads` with the associated waypoint by name.

*   Implement the `FakeWaypointAndEnrolledClients` function in `kubernetes/kubetest/mock.go`:
    *   Accept parameters: `name string`, `cluster string`, `namespace string`.
    *   Return a map of fake Kubernetes clients.
    *   Include a namespace without waypoint labels.
    *   Include a service named `name` labeled with "istio.io/use-waypoint: waypoint".
    *   Include a service named "productpage" without waypoint labels.
    *   Include a deployment named "waypoint" annotated with "gateway.istio.io/managed: istio.io-mesh-controller".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.