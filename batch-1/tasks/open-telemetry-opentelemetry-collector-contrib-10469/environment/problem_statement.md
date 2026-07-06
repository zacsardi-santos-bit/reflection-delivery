## Description

The Datadog exporter currently uses a single fixed strategy to determine the hostname for each resource when exporting OpenTelemetry data. This works fine in general but produces hostnames that differ from what Datadog's native cloud integration agents report — making it harder to correlate data from both pipelines.

We need a way to opt into an alternative hostname resolution mode that matches the conventions used by Datadog's native cloud integrations for AWS EC2, Azure VMs, and GCP instances. Specifically:

- For **AWS EC2 and Azure**, the native integration uses the cloud instance's unique identifier as the primary hostname, rather than the human-readable host name.
- For **GCP**, the native integration uses a combined short-hostname-plus-project-ID format.
- Container IDs should not be used as hostnames in this mode, since they are not meaningful infrastructure-level identifiers.

## Expected Behavior

- The hostname resolution functions for each cloud provider (AWS EC2, Azure, GCP) should accept a flag indicating whether the alternative resolution mode is active.
- When the flag is enabled:
  - Azure: use the VM's unique identifier as the hostname; do not include it as a host alias.
  - EC2: always resolve to the instance identifier.
  - GCP: resolve to a combined format of the short hostname and project/account ID; do not include it as a host alias.
  - Container IDs must not be used as hostnames.
- When the flag is disabled, all existing behavior is preserved.

## Why This Matters

Without this feature, users who collect infrastructure data through both the Datadog native integrations and the OpenTelemetry Collector cannot reliably join or correlate that data, because the hostnames do not match. The alternative mode makes it possible to align the OpenTelemetry pipeline's hostname semantics with the native integration conventions.
