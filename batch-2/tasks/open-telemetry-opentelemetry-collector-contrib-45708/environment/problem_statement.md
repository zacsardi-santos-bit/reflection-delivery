## Description

The load balancing exporter for metrics supports several routing strategies — by service name, metric name, resource identity, and stream ID — but it does not support routing by arbitrary user-defined attributes. Attribute-based routing already exists for trace data, where operators can list one or more attribute keys and the exporter uses the values of those attributes as the routing key, distributing signals consistently across collector backends. Metrics should have the same capability, allowing operators to split metric traffic based on attributes present at the resource, scope, or datapoint level.

In addition to supporting the new routing mode, the exporter should validate its configuration when this mode is selected:
- If the attribute routing mode is enabled but no attribute keys are provided, the exporter must reject the configuration with a clear error.
- If attribute keys are provided but the routing mode is not set to use them, the exporter must also reject the configuration with a clear error explaining what to change.

## Expected Behavior

- Attribute-based routing works for metrics, looking up attribute values from the resource, scope, and datapoint levels (in that order) to form the routing key.
- Configuration is validated: attribute routing without any specified attribute keys is rejected.
- Configuration is validated: specifying attribute keys without enabling attribute routing mode is rejected.
- A valid combination (attribute routing mode + at least one attribute key) succeeds.

## Why This Matters

Without this feature, operators cannot split high-volume metric streams across multiple collector backends using custom labels or attributes. The feature parity gap between traces and metrics forces users to work around the limitation. Proper configuration validation ensures misconfigured exporters fail at startup rather than routing metrics silently to unexpected backends.
