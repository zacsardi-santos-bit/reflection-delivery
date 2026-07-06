## Description

The CosmosDB JavaScript SDK does not support a query execution mode where all backend results must be collected before any sorted output can be returned to the caller. This is necessary for queries that sort by a value computed across all items — for example, ranking results by vector similarity distance. In these cases, it is impossible to determine the final order until every document has been examined, so the results must be buffered and sorted internally before being handed to the application.

## Expected Behavior

- The SDK should provide utility data structures (a comparator-driven map and a fixed-capacity priority queue) to support efficient buffering and deduplication of large result sets during query execution.
- A query execution pipeline component should accumulate all results from the backend, sort them using a configurable ordering, and only then begin returning items to the caller — returning empty placeholders while accumulation is in progress.
- A separate variant of this component should also support distinct (deduplication) behavior alongside the sorted accumulation.
- A utility function should build the set of query features the client advertises to the server. It must include a non-streaming order-by capability flag, but allow callers to suppress that flag when connecting to gateways that do not support the feature.
- The query options type should expose a boolean flag so that applications can opt out of this non-streaming order-by behavior when needed.

## Why This Matters

Without this capability, queries that rely on global sorting (such as vector similarity search) either fail outright or return incorrectly ordered results. Providing this buffered execution path — along with a way to disable it — gives developers both the functionality they need and a safe migration path for environments running older gateway versions.
