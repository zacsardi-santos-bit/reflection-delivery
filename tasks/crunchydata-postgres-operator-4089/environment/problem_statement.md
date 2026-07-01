## Description

The postgres-operator generates OpenTelemetry collector configurations to collect and route logs from cluster components (PostgreSQL, pgBouncer, pgAdmin, pgBackRest, and Patroni). Currently, these generated configurations only output logs to a built-in debug exporter, with no way for operators to route logs to external observability platforms such as Google Cloud Logging. We need to support user-defined exporters specified via the cluster's instrumentation settings.

## Expected Behavior

- When no instrumentation spec is configured, the generated collector configuration should behave as before: using only the built-in debug exporter with empty extensions, processors, and pipelines.
- When an instrumentation spec is provided with custom exporters and log routing configuration, those exporters should be included in the generated collector configuration and all relevant log pipelines should route to those exporters instead of (or in addition to) the default debug exporter.
- The configuration functions that generate collector configs for individual components (pgAdmin, pgBackRest repo host) must accept the instrumentation spec as a parameter so they can incorporate user-defined exporters.
- When custom exporters are configured, appropriate file storage extensions for persistent log reading must be automatically included.
- The Patroni log pipeline must include resource attribute enrichment and attribute grouping processors in all cases.
- The pgAdmin log transform should preserve the original log body, extract the message body, and capture the logger name.

## Why This Matters

Operators running postgres clusters in production need to centralize their logs in external observability systems. Without this feature, there is no supported path for routing postgres-operator-managed component logs to external destinations, making production observability difficult.
