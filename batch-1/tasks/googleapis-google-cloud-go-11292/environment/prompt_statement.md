I'm working on setting up the Google Cloud Pub/Sub client library as a proper new major version module. The v2 directory exists but the code inside it still imports from the original unversioned package paths instead of the new versioned ones. This means the v2 module can't compile on its own — it needs its internal sub-packages (the low-level API client, the protobuf types package, and the in-memory test server) to be accessible under the v2 module path.

There's also a type in the low-level API client package that needs to be renamed — the options struct for configuring the publishing client is currently named after the publisher, but it should be renamed to reflect its broader role managing topic administration.

Finally, the tracing instrumentation embedded in the library identifies itself using the original module name. It should instead report the v2 module's name so that telemetry data correctly reflects which version is in use.

Can you help update the v2 module so that all internal imports use the correct v2 paths, the type is renamed appropriately, and the instrumentation name is updated to match?
