## Description

The MLflow TypeScript tracing SDK currently only supports storing traces in MLflow experiments. For Databricks customers running production AI agents, there is a strong need to store traces directly in Unity Catalog tables instead. Without this capability, teams building agents on Databricks cannot route their trace data to the governed, queryable storage layer that the rest of their data platform uses.

This issue tracks adding Unity Catalog table-prefix support as a first-class trace destination in the TypeScript SDK. The feature includes:

- A new trace location type and accompanying data model for representing Unity Catalog destinations (catalog name, schema name, table prefix)
- Utility helpers to construct, parse, and validate this new location format
- A new trace ID scheme that encodes the UC storage location
- New client methods that interact with the new Databricks trace management API endpoint to persist trace metadata and upload OpenTelemetry spans via OTLP directly to UC tables
- A span processor and exporter pair that manage the full lifecycle: assigning trace IDs, persisting metadata, exporting spans, and propagating tags through to the storage layer
- Integration with the tracing initialization function so that users can configure a Unity Catalog destination at startup

## Expected Behavior

- Developers can specify a Unity Catalog destination (catalog, schema, table prefix) when initializing tracing
- Trace IDs automatically encode the UC location in a new canonical format
- Trace metadata (including user-set tags) is sent to the new Databricks trace management API endpoint
- Spans are uploaded via OTLP to the backend-assigned UC spans table
- Helper utilities allow parsing and constructing both the new and existing trace ID formats
- Experiment-level configuration tags can be used to derive the UC destination automatically
- Serialization and deserialization of trace location data handles the Unity Catalog variant correctly

## Why This Matters

Production AI agent deployments on Databricks commonly use Unity Catalog as their central data platform. Supporting UC as a trace destination lets teams manage their observability data with the same governance, access controls, and query tooling they use for all other data assets.
