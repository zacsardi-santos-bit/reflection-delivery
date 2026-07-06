## Description

The tracing system currently computes LLM call costs on the client side every time a span ends. This approach relies on an optional cost-tracking library that is not included in the minimal tracing package installation. As a result, tests that run against the lightweight tracing SDK fail because the library is not available, and the CI workflow incorrectly installs this library as a workaround.

Beyond the dependency issue, computing costs on the client is unnecessary for non-Databricks backends: those backends can calculate costs themselves when ingesting span data. Only Databricks backends require client-side cost computation because they use a different ingestion path where server-side cost calculation does not occur.

## Expected Behavior

- Cost computation should only happen on the client side when connected to a Databricks backend.
- For all other backends, cost computation should be deferred to the server side, which already has the capability to compute and store cost information.
- The lightweight tracing package should not require the cost-tracking library as a dependency.
- Traces must still show accurate cost information regardless of backend — server-side computation must produce the same cost fields (input cost, output cost, total cost) that were previously computed client-side.

## Why This Matters

This change makes the minimal tracing SDK truly minimal — developers can use it without pulling in an unneeded dependency. It also corrects the logical flow: non-Databricks backends are capable of computing costs during ingestion, so there is no need to duplicate that work on the client.
