Update the end-to-end test infrastructure for the istio/proxy project to resolve compatibility issues with the current Envoy control plane library. Implement necessary changes to configuration formats, server initialization, and logging methods to ensure successful compilation and execution of tests.

*   Implement the `LoadTestData` function in the driver package:
    *   Accept a file path string relative to the test data root.
    *   Return the file contents as a string.
    *   Use this function to load stats configuration, client node metadata, and server node metadata from shared external template files.

*   Update the `XDS` struct in `test/envoye2e/driver/xds.go`:
    *   Implement `Debugf(format string, args ...interface{})` and `Warnf(format string, args ...interface{})` methods for logging.
    *   Ensure these methods satisfy the updated discovery server logger interface.

*   Modify the XDS discovery server initialization:
    *   Pass `context.Background()` as the first argument to `server.NewServer()`.
    *   Follow with the cache and callbacks arguments.

*   Change cache snapshot resource assignment:
    *   Use map-style indexing on the `snap.Resources` field (e.g., `snap.Resources[cache.Cluster]`).

*   Remove deprecated Envoy startup flag:
    *   Do not include the `--allow-unknown-fields` flag in Envoy process startup arguments.

*   Update Envoy listener filter configurations in test templates:
    *   Use `typed_config` with an `@type` field specifying the fully-qualified protobuf type URL (`type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager`).

*   Adjust Wasm HTTP filter plugin configurations:
    *   Wrap using `typed_config` with `@type` set to `type.googleapis.com/udpa.type.v1.TypedStruct`.
    *   Include `type_url` set to `envoy.extensions.filters.http.wasm.v3.Wasm`, with the filter config nested under a `value` key.

*   Correct the source_canonical_service metric label:
    *   Set the label for the outbound (client) node to `productpage-v1`.

*   Ensure side-effect imports in the driver package:
    *   Preload proto definitions for UDPA types and the HTTP connection manager v3 extension.

*   Populate test package variables using `LoadTestData`:
    *   Use paths `testdata/bootstrap/stats.yaml.tmpl`, `testdata/client_node_metadata.json.tmpl`, and `testdata/server_node_metadata.json.tmpl` for `statsConfig`, `outboundNodeMetadata`, and `inboundNodeMetadata` respectively.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.