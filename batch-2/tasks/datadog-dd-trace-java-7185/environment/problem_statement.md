## Description

Teams that run Java services instrumented with both the Datadog agent and OpenTelemetry-based configuration currently have to duplicate their settings in two different formats. The Datadog agent does not recognize OpenTelemetry environment variables or system properties, so every shared concern — service name, propagation style, sampling rate, data exporters, HTTP header capture, and resource attributes — must be configured separately in Datadog's own format.

We need a bridge component inside the Datadog agent that, when users explicitly opt in to OpenTelemetry compatibility mode, automatically reads standard OpenTelemetry environment variables and system properties and translates them into the equivalent Datadog configuration.

## Expected Behavior

- When the OpenTelemetry compatibility layer is **not** opted into, all OTel-specific settings are completely ignored and have no effect on Datadog configuration.
- When the compatibility layer **is** opted into (via an explicit enable flag), the following translations must occur:
  - Service name, environment, version, and custom resource attributes are extracted from the standard OTel resource attribute settings.
  - A dedicated service name property overrides the service name inside the general resource attributes map.
  - Propagation style names are translated, with abbreviated format identifiers normalized to their explicit single-header variant equivalents.
  - Sampling configuration maps to Datadog's sample rate setting.
  - Setting an exporter to a disabled/no-output state turns off the corresponding Datadog data collection (traces or metrics).
  - HTTP request and response header capture settings from both client and server OTel properties are merged and reformatted as Datadog header tag mappings.
  - The OTel Java agent extensions path maps to Datadog's extension path setting.
  - The log level from OTel environment variables maps to Datadog's log level setting.
  - Custom resource attributes beyond the reserved keys are mapped to Datadog tags, limited to the first 10 entries.

## Why This Matters

This removes the need for dual configuration for teams adopting OpenTelemetry alongside Datadog, reducing operational overhead and configuration drift.
