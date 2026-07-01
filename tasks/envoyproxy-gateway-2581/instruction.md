Implement support for TCP keepalive settings on upstream connections in the xDS translation layer of the Envoy Gateway project. Ensure that these settings are correctly translated from the intermediate representation to the Envoy cluster configuration, reflecting the configured probe count, idle time, and probe interval.

*   Update the `HTTPRoute` struct in `internal/ir/xds.go`:
    *   Add a new optional field `TCPKeepalive *TCPKeepalive` serialized as `tcpKeepalive` in JSON and YAML.
*   Modify the deep-copy functionality:
    *   In `internal/ir/zz_generated.deepcopy.go`, ensure `HTTPRoute.DeepCopyInto` handles the new `TCPKeepalive` pointer field, allocating and deep-copying it when non-nil.
*   Enhance the `xdsClusterArgs` struct in `internal/xds/translator/cluster.go`:
    *   Add a new field `tcpkeepalive *ir.TCPKeepalive` to pass TCP keepalive settings into cluster construction.
*   Implement the `buildXdsClusterUpstreamOptions` function in `internal/xds/translator/cluster.go`:
    *   Accept a `*ir.TCPKeepalive` argument and return a `*clusterv3.UpstreamConnectionOptions`.
    *   Return nil if the argument is nil.
    *   Construct `UpstreamConnectionOptions` with `TcpKeepalive` sub-field populated from `ir.TCPKeepalive` fields.
*   Update the `buildXdsCluster` function in `internal/xds/translator/cluster.go`:
    *   Check if `args.tcpkeepalive` is non-nil and assign `cluster.UpstreamConnectionOptions` using `buildXdsClusterUpstreamOptions(args.tcpkeepalive)`.
*   Modify the `processXdsCluster` helper in `internal/xds/translator/translator.go`:
    *   Populate the `tcpkeepalive` field of `xdsClusterArgs` with `httpRoute.TCPKeepalive`.
*   Create a test-data input file:
    *   Location: `internal/xds/translator/testdata/in/xds-ir/upstream-tcpkeepalive.yaml`.
    *   Include an HTTP listener definition with at least one route having a `tcpKeepalive` block with `idleTime`, `interval`, and `probes` set.
*   Generate golden output files:
    *   `internal/xds/translator/testdata/out/xds-ir/upstream-tcpkeepalive.clusters.yaml` must include an `upstreamConnectionOptions.tcpKeepalive` block with `keepaliveProbes`, `keepaliveTime`, and `keepaliveInterval`.
    *   Create additional files: `upstream-tcpkeepalive.endpoints.yaml`, `upstream-tcpkeepalive.listeners.yaml`, and `upstream-tcpkeepalive.routes.yaml`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.