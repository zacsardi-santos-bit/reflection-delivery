## Description

The Cassandra span writer currently depends on an external tracing library for its core data types (spans, trace IDs, processes, tags, logs, references). This creates an unnecessary coupling that makes the storage layer harder to maintain and evolve independently. We should refactor the write path to work directly with the Cassandra-native internal model, which uses simpler primitive types for timestamps, durations, and identifiers, and represents processes as value types rather than pointers.

As part of this refactoring, the write interface can be simplified: the context parameter on the write method is unused and should be removed.

Additionally, there is currently no direct write path for the newer OpenTelemetry-formatted traces in the v2 storage layer. We need a trace writer that accepts OTel trace batches, converts them into the Cassandra internal data model, and writes each span individually — collecting and reporting all per-span errors together.

Finally, a utility function is needed to look up span kind from a span's tag list, since this information is required for operation indexing in the write path.

## Expected Behavior

- The Cassandra span writer accepts the internal database model span type directly (no conversion from external types)
- A new interface captures the span writer's core write and close operations
- A new writer for the v2 storage layer accepts OpenTelemetry traces, converts them to the internal model, and writes each span while aggregating any errors
- A helper to retrieve span kind from span tags is available in the database model package

## Why This Matters

Removing the external library dependency from the write path reduces the surface area that needs updating when the data model evolves and makes the storage layer more self-contained.
