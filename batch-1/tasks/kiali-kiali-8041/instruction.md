Update the hashing algorithm used for generating identifiers in the service graph and mesh graph API responses to SHA-256, ensuring all identifiers are 64-character hexadecimal strings. Modify the relevant functions and update the expected golden test data files accordingly.

*   Change the hashing algorithm in the following functions to SHA-256:
    *   `nodeHash(id string) string` in `graph/config/cytoscape/cytoscape.go`
    *   `edgeHash(from, to, protocol string) string` in `graph/config/cytoscape/cytoscape.go`
    *   `timeSeriesHash(cluster, serviceNs, service, workloadNs, workload, app, version string) string` in `graph/telemetry/istio/istio.go`
    *   `timeSeriesHash(cluster, serviceNs, service, workloadNs, workload, app, version string) string` in `graph/telemetry/istio/appender/extensions.go`
    *   `nodeHash(id string) string` in `mesh/config/cytoscape/cytoscape.go`
    *   `edgeHash(from, to string) string` in `mesh/config/cytoscape/cytoscape.go`
    *   `timeSeriesHash(cluster, namespace, name string) string` in `mesh/generator/generator.go`
*   Update all expected golden test data files:
    *   Located in `graph/api/testdata/`:
        *   `test_app_graph.expected`
        *   `test_versioned_app_graph.expected`
        *   `test_service_graph.expected`
        *   `test_workload_graph.expected`
        *   `test_rates_sent_graph.expected`
        *   `test_rates_received_graph.expected`
        *   `test_rates_total_graph.expected`
        *   `test_rates_none_graph.expected`
        *   `test_workload_node_graph.expected`
        *   `test_app_node_graph.expected`
        *   `test_versioned_app_node_graph.expected`
        *   `test_service_node_graph.expected`
        *   `test_rates_node_graph_total.expected`
        *   `test_complex_graph.expected`
        *   `test_mc_source_graph.expected`
        *   `test_ambient_graph.expected`
    *   Located in `mesh/api/testdata/`:
        *   `test_mesh_graph.expected`
    *   Ensure all node and edge 'id' fields contain 64-character SHA-256-based hex strings.
    *   Remove any trailing newline characters, ensuring the final character is the closing brace '}'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.