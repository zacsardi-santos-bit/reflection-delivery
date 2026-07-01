## Description

Several tools in the CLI need to request structured, JSON-formatted responses from the underlying AI model. Currently, each tool that does this has to implement its own logic for things like applying sensible request defaults, retrying on transient failures, cleaning up model responses that accidentally include markdown formatting, and reporting errors and telemetry. This leads to duplicated, inconsistent behavior across tools.

We need a centralized, reusable AI client for making JSON-structured model requests. This client should apply consistent defaults, wrap requests in a retry mechanism, automatically clean markdown wrappers from model output when present (and log telemetry in that case), and handle errors consistently — including distinguishing between user-aborted requests (which should not be reported as errors) and actual failures.

The configuration system should initialize and expose this new client after authentication is complete, so tools can access it through the config.

Additionally, the utility that fixes failed file edit operations should be updated to use this new shared client. It should also support tracking individual requests through async context propagation (so each request carries a traceable ID), fall back gracefully with a warning when no tracking context is available, and cache results to avoid redundant model calls for identical inputs.

## Expected Behavior

- Attempting to access the new LLM client before authentication has completed should produce a clear error message indicating it has not yet been initialized.
- After authentication, the client should be accessible and ready to use.
- The client should correctly apply default generation parameters and allow them to be overridden per request.
- System instructions should be included in requests only when explicitly provided.
- Responses wrapped in markdown code fences should be cleaned automatically, with telemetry logged when this occurs.
- Plain whitespace around a JSON response should be trimmed without triggering malformed-response telemetry.
- Specific, actionable error messages should be thrown for empty responses, unparseable responses, and generic API failures.
- Aborted requests should propagate the abort error without triggering error reporting.
- The edit-fixing utility should use prompt IDs from async context when available, warn and fall back to a generated ID when not, cache results for repeated identical calls, and make fresh requests when any parameter differs.

## Why This Matters

Centralizing AI model request logic reduces duplication, ensures consistent retry/error behavior across all tools, and makes it easier to add cross-cutting concerns like telemetry and caching in one place.
