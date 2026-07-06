Implement a new controller in the Tailscale Kubernetes operator to automatically manage DNS records for Tailscale proxy services within the cluster. Ensure that DNS records are kept up to date with the current IP addresses of proxy pods and are stored in a shared configuration resource for the cluster's nameserver.

*   Define the `dnsRecordsReconciler` struct in `cmd/k8s-operator/dnsrecords.go` with:
    *   An embedded `controller-runtime` client.
    *   A logger of type `*zap.SugaredLogger`.
    *   A `tsNamespace` string field.
    *   Implement the `Reconcile` method with the signature:
        ```go
        func (dnsRR *dnsRecordsReconciler) Reconcile(ctx context.Context, req reconcile.Request) (res reconcile.Result, err error)
        ```

*   Implement reconciliation logic for headless services:
    *   For services with `LabelParentType` set to 'svc' and a `tailscale.com/tailnet-fqdn` annotation, map the FQDN to proxy pod IPs from the `EndpointSlice` into the `dnsrecords` ConfigMap.
    *   For services with `LabelParentType` set to 'ingress', map the ingress's load balancer hostname to proxy pod IPs from the `EndpointSlice` into the `dnsrecords` ConfigMap.

*   Update DNS records in the `dnsrecords` ConfigMap:
    *   When the `tailscale.com/tailnet-fqdn` annotation changes, replace the old FQDN mapping with the new one.
    *   When the ingress load balancer hostname changes, update the hostname mapping.
    *   When proxy pod IP addresses change (i.e., `EndpointSlice` addresses change).

*   Ensure DNS records are only created or updated if:
    *   Exactly one `DNSConfig` resource exists with the nameserver ready condition set to true.

*   Store DNS records in a ConfigMap:
    *   Name the ConfigMap `dnsrecords` in the operator's namespace (`tsNamespace`).
    *   Use the data key `records.json` to store JSON-marshaled `Records` struct.

*   Define the `Records` struct in the `tailscale.com/k8s-operator` package:
    *   Include an `IP4` field of type `map[string][]string` for DNS hostnames to IP address mappings.

*   Define and export constants in the `tailscale.com/k8s-operator` package:
    *   `DNSRecordsCMName` with the value "dnsrecords".
    *   `DNSRecordsCMKey` with the value "records.json".

*   Rename the `DNSConfig` status field:
    *   Change the field name to `Nameserver` with Go type `*NameserverStatus` and JSON tag `nameserver`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.