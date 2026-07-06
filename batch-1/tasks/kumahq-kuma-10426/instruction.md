Implement a background component to periodically update the available services for all ingress proxies in a multizone service mesh setup. Ensure this component operates independently of the ingress connection status and allows configuration of the update interval.

*   Update the multizone zone configuration:
    *   Add a duration field 'ingressUpdateInterval' under 'multizone.zone' in the YAML config.
    *   Support configuration via the environment variable KUMA_MULTIZONE_ZONE_INGRESS_UPDATE_INTERVAL.

*   Refactor the GetAvailableServices function in pkg/xds/ingress:
    *   Ensure it is a pure function that accepts lists of dataplanes, mesh gateways, external services, and tag filters.
    *   Return a slice of *mesh_proto.ZoneIngress_AvailableService without performing any store updates.

*   Implement the NewZoneAvailableServicesTracker constructor in pkg/zone:
    *   Accept parameters: logger, metrics, resource manager, mesh cache, update interval, ingress tag filters, and zone name.
    *   Return a ZoneAvailableServicesTracker instance and an error.

*   Ensure the ZoneAvailableServicesTracker:
    *   Implements Start(stop <-chan struct{}) error to run until the stop channel is closed.
    *   Periodically computes available services and updates the AvailableServices field on all ZoneIngress resources.
    *   Updates all ZoneIngress resources regardless of their online status.
    *   Reflects an empty AvailableServices list when all dataplanes for a mesh are removed.

*   Modify the ZoneIngressProxy.ZoneIngressResource.Spec in the XDS proxy builder:
    *   Populate the Zone and Networking fields.
    *   Remove the requirement to set the AvailableServices field.

*   Update the test framework:
    *   Export the constant UniversalZoneIngressPort with a value of 30686.
    *   Provide a MultipleIngressUniversal function to install a zone ingress with a unique name based on the advertised port.
    *   Ensure the UniversalApp type includes KillMainApp() and StartMainApp() methods for process control.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.