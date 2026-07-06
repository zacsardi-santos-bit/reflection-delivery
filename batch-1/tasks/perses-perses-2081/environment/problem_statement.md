## Description

Perses supports automatic service discovery (e.g., via Kubernetes or HTTP endpoints) to find available datasources. However, when a datasource service is discovered, there is currently no mechanism to automatically construct a valid, schema-conformant datasource plugin configuration from the datasource's type schema. This gap means that discovered services cannot be turned into usable global datasources without hardcoding the schema structure for each plugin type.

## Expected Behavior

- A new utility package should be able to read a datasource plugin's CUE schema and produce a structured tree representation of that schema's fields. Optional fields should be excluded from this representation.
- The tree representation must capture field names, field types (string, bool, integer, float, struct), and any concrete (fixed) values that appear directly in the schema.
- A second utility should be able to combine a tree representation with a discovered HTTP proxy endpoint to produce a complete, ready-to-use datasource plugin object. The proxy URL and related config should be injected into the correct location within the spec based on markers found in the schema tree.
- Both Prometheus and Tempo datasource schemas should be supported and produce the correct tree structures.

## Why This Matters

Service discovery is only useful if the discovered endpoints can be automatically turned into registered datasources. Without the ability to derive plugin configurations directly from schemas, administrators must manually create datasources even when the discovery mechanism has already located the backing services. This new utility closes that gap and enables fully automated datasource provisioning.
