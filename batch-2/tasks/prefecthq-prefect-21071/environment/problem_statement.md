## Description

Prefect currently has no way to collect or report system resource metrics (CPU, memory, etc.) during flow run execution. It would be valuable to automatically gather these metrics and export them via the standard OpenTelemetry protocol so that operators can observe resource usage alongside other telemetry signals.

## Expected Behavior

- Prefect should automatically collect system resource metrics while a flow run is active and export them to an OpenTelemetry-compatible endpoint.
- Users should be able to enable or disable this feature via a Prefect setting, and separately configure the collection interval.
- The metrics endpoint should be resolved from standard OpenTelemetry environment variables when set, allowing users to point metrics at any compatible collector. A metrics-specific endpoint variable should take priority over a generic one.
- When pointing to Prefect Cloud, authentication should be handled automatically using the configured API key.
- When a custom endpoint is specified via environment variable, Prefect must **not** inject any Prefect-specific authentication headers — this ensures API keys are never leaked to third-party collectors, and existing OpenTelemetry header environment variables are still respected.
- If the required libraries are not installed, or if the endpoint is misconfigured, the feature should silently degrade to a no-op without interrupting the flow run.

## Why This Matters

Observability into resource consumption during flow execution helps users diagnose performance issues, plan capacity, and understand the health of their workflows. Without this, operators have no standardized, automatic way to correlate flow execution with system resource data.
