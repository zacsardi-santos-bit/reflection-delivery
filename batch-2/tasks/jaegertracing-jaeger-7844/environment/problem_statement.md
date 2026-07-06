## Description

The Cassandra storage backend in Jaeger currently converts trace data by routing it through an intermediate protobuf-based representation, even though the actual target storage schema is Cassandra-native. This creates an unnecessary coupling: the conversion layer depends on a data model designed for network transmission rather than the one actually used for persistence. The result is extra complexity and an impedance mismatch between what the conversion layer produces and what Cassandra stores.

Additionally, several constants in the Cassandra data model package that identify value types (such as string, boolean, 64-bit integer, 64-bit floating-point, and binary) are unexported. This prevents other packages in the storage layer from referencing them without duplicating the values, which is a maintenance problem and a design inconsistency.

## Expected Behavior

- The constants representing value types and span reference types in the Cassandra data model package should be publicly accessible so they can be used across the storage layer without copying.
- The conversion functions that translate between OTel trace data and Cassandra storage should work directly with the Cassandra-native data model types, without routing through the protobuf model.
- The timestamp representation in log entries should use a numeric type (integer microseconds) consistent with what Cassandra stores, rather than a time-struct type.
- When converting to the Cassandra model, if no service name is available in the source data, a well-known default placeholder should be used for the process service name.
- When converting from the Cassandra model, unknown tag value types should produce a human-readable fallback string attribute rather than failing silently.

## Why This Matters

Removing the protobuf intermediary from the Cassandra conversion layer simplifies the code, reduces cross-package dependencies, and ensures that the data model used throughout the Cassandra storage path is consistent and self-contained. Exporting the type constants enables other packages to correctly interpret and work with data read from or written to Cassandra.
