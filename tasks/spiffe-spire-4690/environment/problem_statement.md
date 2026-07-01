## Description

The SPIRE server's CA manager currently stores its certificate authority journal data exclusively on disk. This means there is no centralized, queryable record of CA journal state in the datastore, and old journal data that is no longer needed is never cleaned up. Over time, stale journal data accumulates with no mechanism to prune it.

## Expected Behavior

- CA journal data should be persisted in the datastore in addition to being stored on disk. When the server starts up and finds a journal on disk but not in the datastore, it should load from disk and begin dual-writing going forward.
- The datastore should expose operations to create/update a CA journal, fetch a CA journal by its active authority identifier, prune old CA journals whose certificate authorities have all expired past a safety threshold, and list all CA journals (for testing purposes).
- The CA rotator should periodically prune CA journals that are no longer needed (i.e., all their certificate authority entries have expired past a configurable safety threshold), keeping the datastore clean.
- All CA rotation and activation operations (rotate, activate for both X.509 CAs and JWT keys) should accept and propagate context, enabling proper cancellation and timeout handling.
- The telemetry layer should emit metrics for all new datastore operations related to CA journals.
- When saving a journal fails in the datastore, the error should be clearly reported, while the on-disk copy should still be updated successfully.

## Why This Matters

Without datastore-backed CA journals, operators have no reliable way to inspect or manage CA journal state across a SPIRE deployment. Stale journals accumulate indefinitely on disk without any automated cleanup, which creates operational burden and potential confusion during CA rotation.
