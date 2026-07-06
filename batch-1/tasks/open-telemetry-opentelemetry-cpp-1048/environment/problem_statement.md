## Description

The OpenTelemetry C++ SDK currently supports exporting traces via gRPC and logs via HTTP, but there is no way to export log records over gRPC using the OTLP protocol. Developers who want to send logs to an OpenTelemetry Collector over gRPC — the same way they currently export traces — have no supported path to do so.

## Expected Behavior

- A new log exporter component should be available that connects to an OpenTelemetry Collector via gRPC and transmits log records in OTLP format.
- The exporter should integrate seamlessly with the existing log provider and batch processor infrastructure, so that logs emitted through a logger can be automatically collected and forwarded over gRPC.
- Successful gRPC transmissions should be reported as a successful export, and failed or cancelled gRPC calls should be reported as a failed export.
- Once the exporter is shut down, further export attempts should consistently fail rather than silently dropping data or behaving unpredictably.
- Log records should carry full context including trace and span identifiers, trace sampling flags, severity level, name, message body, and arbitrary typed attributes (booleans, integers of various sizes, floating-point values, strings, and arrays of each).
- The exporter configuration options (endpoint, SSL credentials, timeout, metadata headers) should be shared with the existing gRPC trace exporter.

## Why This Matters

Unified telemetry collection — traces, metrics, and logs — is a core goal of OpenTelemetry. Without a gRPC-based log exporter, users who rely on gRPC as their transport layer must use a different protocol for logs than for traces, complicating their collector configuration and deployment. Adding this exporter closes the gap and lets developers use a consistent gRPC transport for all signal types.
