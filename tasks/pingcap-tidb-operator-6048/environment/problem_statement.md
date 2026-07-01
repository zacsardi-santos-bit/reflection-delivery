## Description

Bootstrap SQL — the SQL statements that run when a TiDB cluster is first initialized — is currently configured as a property of the TiDB component's security settings. This is semantically incorrect: bootstrap SQL is a cluster-wide initialization concern, not a per-instance security property. The misplacement makes the feature harder to discover and reason about, and couples initialization logic to component-level security configuration where it does not belong.

## Expected Behavior

- The bootstrap SQL configuration should be a top-level field on the cluster specification, not nested inside the TiDB component's security settings.
- Any code that currently reads bootstrap SQL from the TiDB component security configuration should be updated to read it from the cluster spec instead.
- The TiDB component's security type should no longer expose a bootstrap SQL field.

## Why This Matters

Placing bootstrap SQL at the cluster level reflects its true scope — it is an initialization step for the entire cluster, not a security concern of any individual TiDB group. This makes the API more intuitive for operators configuring clusters, and separates initialization logic from security configuration.
