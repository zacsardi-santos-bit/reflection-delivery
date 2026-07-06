Implement the functionality to resolve service URLs using both the older "extensions" and the newer "networking.v1" ingress API groups in a Kubernetes client library. Ensure the resolver checks for API group support and handles TLS configurations appropriately. Update utility methods to correctly handle environment variables, system properties, and service port lookups.

*   Implement `URLFromEnvVarsImpl` class:
    *   Provide a no-arg constructor.
    *   Implement `getURL(Service svc, String portName, String namespace, KubernetesClient client) -> String`:
        *   Return null if no service-related environment variables, system properties, or expose annotations are present.
        *   Return a URL in the format '<protocol>://<host>:<port>' when all relevant system properties are set.
        *   Return the value of the 'fabric8.io/exposeUrl' annotation if present and non-empty.
    *   Implement `getPriority() -> int` to return `ServiceToURLProvider.ServiceToUrlImplPriority.THIRD.getValue()`.

*   Implement `URLFromIngressImpl` class:
    *   Provide a no-arg constructor.
    *   Implement `getURL(Service svc, String portName, String namespace, KubernetesClient client) -> String`:
        *   Throw `RuntimeException` with message "Couldn't find port: <portName> for service <serviceName>" if no matching port is found.
        *   Use `client.supports()` to check for support of both ingress types:
            *   Use extensions ingress list if supported.
            *   Use networking.v1 ingress list if supported (and extensions is not).
            *   Return null if neither is supported.
        *   Return an HTTPS URL if TLS is configured for the matching host; otherwise, return an HTTP URL.
    *   Implement `getPriority() -> int` to return `ServiceToURLProvider.ServiceToUrlImplPriority.FIRST.getValue()`.

*   Implement `URLFromServiceUtil` utility methods:
    *   `resolveHostFromEnvVarOrSystemProperty(String serviceName) -> String`: Look up and return the value of `<SERVICENAME>_SERVICE_HOST`.
    *   `resolveProtocolFromEnvVarOrSystemProperty(String serviceName, String port) -> String`: Look up and return the value of `<SERVICENAME>_PORT_<PORT>_TCP_PROTO`.
    *   `resolvePortFromEnvVarOrSystemProperty(String serviceName, String portName) -> String`: Look up and return the value of `<SERVICENAME>_SERVICE_PORT`.
    *   `getURLFromTLSHost(String host, String pathPostFix) -> String`: Return "https://<host><pathPostFix>" if host is non-empty; return null if host is empty.
    *   `getURLFromNetworkingV1IngressList(List<Ingress> ingressList, String namespace, String serviceName, ServicePort port) -> String`:
        *   Return null if the list is empty, ingress has no rules, rules have no paths, or the matching host is empty.
        *   Return an HTTPS URL if TLS is configured for the matching host; otherwise, return an HTTP URL.
    *   `getURLFromExtensionsV1beta1IngressList(List<Ingress> ingressList, String namespace, String serviceName, ServicePort port) -> String`:
        *   Return null if the list is empty, ingress has no rules, rules have no paths, or the matching host is empty.
        *   Return an HTTP URL when a valid ingress backend is found without TLS.
    *   `getServicePortByName(Service service, String portName) -> ServicePort`:
        *   Return null if the service has no ports.
        *   Return the first port if `portName` is an empty string.
        *   Return the port matching `portName` if `portName` is non-empty.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.