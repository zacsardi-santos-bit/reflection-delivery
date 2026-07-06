## Description

The OpenTelemetry Collector currently emits its own internal telemetry metrics using unit strings expressed in plural form — for example, units like "records", "spans", "datapoints", "samples", "batches", "times", "items", "units", "combinations", and "requests". However, the OpenTelemetry specification requires that metric unit identifiers be in singular form.

This mismatch causes the Collector's own self-monitoring metrics to be non-conformant with the specification, which can cause issues when integrated with tooling that validates metric metadata against OTel conventions.

## Expected Behavior

- All metric unit strings in the Collector should use singular form (e.g., "record" instead of "records", "span" instead of "spans", "datapoint" instead of "datapoints")
- The change affects metrics across all major Collector components: receivers, exporters, processors, and scrapers
- This is a breaking change to the emitted metric metadata — anyone relying on the exact unit strings will need to update

## Why This Matters

Conforming to the OpenTelemetry specification for unit naming is important for interoperability. Downstream observability platforms and tooling that enforce or validate OTel conventions will correctly recognize the Collector's metrics only when those metrics are specification-compliant. Fixing this ensures the Collector's self-monitoring is first-class OTel-compatible telemetry.
