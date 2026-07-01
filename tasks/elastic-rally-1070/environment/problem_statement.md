## Description

When running benchmark operations in Rally, it is not currently possible to specify custom HTTP timeouts, custom request headers, or opaque request identifiers on a per-operation basis through the track configuration. This is a problem for users who need fine-grained control over how each operation communicates with Elasticsearch — for example, when operations need to carry tracing headers, routing information, or identifiers that are required by Elasticsearch plugins or middleware.

## Expected Behavior

- Benchmark operations (such as bulk indexing, querying, force-merging, creating indices, checking cluster health, running raw requests, and retrieving index stats) should all accept a request timeout, custom HTTP headers, and an opaque identifier as configuration values.
- When these values are provided in the operation parameters, they must be forwarded to the underlying Elasticsearch API call — the timeout as a numeric value, the headers as a dictionary, and the opaque identifier as a dedicated parameter or merged into the headers as appropriate depending on whether the operation uses the high-level API client or low-level transport layer.
- When a value is not provided, it should not be sent at all (not passed as a null value).
- The search parameter source should also surface these new fields so they are available to the query runner. These fields should always be present in the output, defaulting to a null/absent value when not configured.

## Why This Matters

Many Elasticsearch deployments use opaque identifiers and custom headers for request tracing, correlation, and routing. Without the ability to set these values in Rally's track operations, benchmark results cannot replicate real-world workloads that depend on these HTTP-level controls. This change makes benchmarks more representative and lets operators test Elasticsearch behavior under conditions that match their production setup.
