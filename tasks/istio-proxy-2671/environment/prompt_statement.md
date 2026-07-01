I'm working on updating the end-to-end test infrastructure for the istio/proxy project and running into several compatibility issues after upgrading the Envoy control plane library.

First, the discovery server initialization in the test driver no longer compiles — the library now requires a context to be passed as the first argument, and the snapshot cache API for setting cluster and listener resources has changed from named struct fields to map-based indexing. The logger interface also requires two additional methods that aren't currently implemented.

Second, all the Envoy configuration templates used in the tests are using the old untyped filter configuration format, but the current Envoy build requires filters to be configured using the typed format with a fully-qualified protobuf type URL. This affects both the HTTP connection manager filter and Wasm plugin filters, which also need to be wrapped in a typed struct envelope. A deprecated Envoy command-line flag also needs to be removed since it's no longer valid.

Third, there's a maintenance issue: large blocks of node metadata JSON and stats configuration YAML are copy-pasted as inline string constants in multiple test files. These should instead be loaded from shared external template files using a helper function, so they can be maintained in one place. I need to introduce or use a function that reads these files from the testdata directory and returns their contents as strings.

Finally, one of the metric label assertions has the wrong value for the canonical service name of the outbound proxy node — it's currently set to a short app name but should be the full workload name.
