## Description

The current snapshot storage configuration is expressed as a single optional field that, when present, enables cloud storage and provides credentials, or when absent, falls back to local filesystem storage. This design has two problems: the intent is implicit (absence of a field means "use local"), and it is fundamentally not extensible — adding support for additional cloud storage backends would require adding more optional top-level fields.

We need to replace this with a structured, explicit snapshot storage configuration that names the desired backend clearly and nests backend-specific credentials underneath it.

## Expected Behavior

- Snapshot storage configuration must be expressed under a dedicated configuration section rather than as a bare cloud-provider field at the top level.
- Within that section, the storage backend type must be explicitly named (e.g., "local" or "s3").
- Cloud storage credentials (bucket, region, access key, secret key, endpoint URL) must be nested under their own sub-section within the snapshot configuration block.
- The default configuration (when nothing is specified) must produce local filesystem storage behavior, equivalent to the previous "no cloud config provided" state.
- All code referencing the old configuration field must be updated to use the new structure.

## Why This Matters

This change makes the configuration intent explicit and readable, reduces ambiguity around defaults, and lays the groundwork for adding further storage backends (such as Google Cloud Storage or Azure) without proliferating optional top-level config fields.
