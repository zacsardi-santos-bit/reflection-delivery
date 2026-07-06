## Description

The attack paths feature currently only supports executing predefined queries against graph data. Users have no way to run ad-hoc graph queries to explore relationships or data structures that aren't covered by the built-in query set. Additionally, there is no way to discover metadata about the underlying graph schema — specifically the cloud provider version and links to the official schema documentation — which makes it difficult to write meaningful custom queries.

Two new capabilities are needed:

1. **Custom query endpoint** — a new endpoint that accepts an arbitrary read-only graph query, executes it against the provider's graph database, and returns the matching nodes and relationships. The results should be filtered to the scan's provider and automatically truncated to a configurable maximum number of nodes. Write queries must be rejected with a permission error. If the graph data for the scan is not yet ready, the endpoint should return an appropriate error.

2. **Graph schema metadata endpoint** — a new read-only endpoint that returns the cloud provider, graph library version in use, and URL links to the schema documentation (both a browsable GitHub URL and a raw file URL). If no schema metadata is available for the provider, the endpoint should indicate that with a not-found response.

## Expected Behavior

- Submitting a read-only graph query against a ready scan returns a graph result including a node list, relationship list, total node count, and a flag indicating whether the result was truncated.
- Submitting a write query is rejected with a 403 response.
- Querying when graph data is not yet available returns a 400 response indicating data is not available.
- An empty result set (no nodes) returns a 404 response.
- The schema endpoint returns provider name, version, and two documentation URLs when metadata is available.
- The schema endpoint returns 404 with a message about missing schema metadata when no schema metadata record exists.
- Existing graph query result objects must also include total node count and truncation flag fields.

## Why This Matters

Without custom query support, users are limited to a fixed set of predefined queries and cannot explore the graph freely. Without schema discovery, users cannot easily understand the data model, making it hard to write effective queries.
