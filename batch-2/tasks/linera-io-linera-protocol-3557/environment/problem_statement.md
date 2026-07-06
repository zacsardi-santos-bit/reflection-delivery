## Description

The validator server and its shards should integrate with a continuous profiling service to collect performance data during operation. Currently, the configuration structures for both individual shards and the validator as a whole have no fields for specifying where the profiling service is located. This means there is no way to connect the server to a profiling backend at startup.

## Expected Behavior

- The shard configuration structure must include a required field for the profiling server hostname. Attempting to load a shard configuration that omits this field should fail with a parse error rather than silently succeeding.
- The validator-level configuration structure must also include required fields for the profiling server hostname and port. Loading validator configuration without these fields should likewise fail.
- The function that generates shard configurations from a template must accept and apply profiling server connection details, populating each generated shard's configuration with the appropriate profiling server address and port.
- All existing configuration files and test fixtures used to validate the configuration structures must be updated to include the new required profiling fields so they remain valid.

## Why This Matters

Without required profiling configuration, it is easy to deploy a validator node that silently skips profiling — there is no signal that profiling is misconfigured. Making these fields required ensures that every deployment explicitly declares its profiling setup, and that a missing configuration is caught at startup rather than discovered later through missing profiling data.
