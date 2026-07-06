I'm working on the Perses monitoring platform and need to add a utility package that bridges the gap between CUE schema definitions and usable datasource plugin configurations in the service discovery module.

Right now, when the discovery module finds a datasource service (for example, via Kubernetes), there's no way to automatically construct the plugin configuration from the schema — we'd need to hardcode each plugin's structure. Instead, I'd like to be able to read any datasource's CUE schema and derive a structured tree of fields from it, and then use that tree plus a discovered proxy endpoint to produce a fully-formed plugin configuration object.

The tree should capture field names, their types (string, boolean, integer, float, or struct), and any fixed/concrete values already defined in the schema. Optional fields should be excluded. The tree should also support being sorted by field name for deterministic processing.

Once a tree is built from a schema, I need a way to combine it with an HTTP proxy configuration so that the proxy gets injected into the right place in the spec (identified by a proxy kind marker in the schema). The output should be a plugin object with the kind set from the schema's concrete kind value and the spec populated with the schema's field structure and the injected proxy.

This should work correctly for at least Prometheus and Tempo datasource schemas, which both follow the standard structure of having a top-level kind and spec.
