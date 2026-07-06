## Description

MLflow's LLM judge system does not currently support routing model API calls through a custom proxy server, or injecting additional HTTP headers when invoking the underlying language model. This is a significant gap for teams operating in enterprise environments where all outbound API traffic must pass through a corporate proxy, or where LLM providers require custom authentication headers beyond standard API keys.

There should be a way to specify:
1. A custom base URL (proxy endpoint) for the judge's model calls
2. Extra HTTP headers (e.g., authentication tokens, routing headers) to include in each request

## Expected Behavior

- Users can provide a custom base URL and/or extra HTTP headers when creating a judge
- Both options should be available programmatically and via the command-line interface
- Input validation should catch common mistakes: non-string base URLs, non-dictionary header objects, and headers with non-string values, each with a clear error message
- Because these values may contain sensitive credentials, they must **not** be persisted when a judge is registered — a round-trip serialization/deserialization cycle should result in empty/unset values for both fields
- The string representation of a judge should display the base URL (with credentials and query parameters stripped for safety) and header keys (but never header values)
- Providers that manage their own routing (e.g., Databricks managed judges, Databricks/deployment endpoints) should reject these options with a clear error message, since they cannot be meaningfully applied to internally-routed endpoints

## Why This Matters

Teams using MLflow's evaluation and judging capabilities in locked-down or proxy-dependent environments are currently blocked from using LLM judges at all. This change enables those use cases while maintaining security by not storing credentials.
