## Description

The Delta Lake kernel does not currently support spatial data types. Specifically, there is no way to declare a Delta table column (or nested field) whose type is geometry (for planar spatial data) or geography (for geodetic/spherical spatial data). These are increasingly common in geospatial workloads, and their absence forces users to fall back to untyped representations.

## Expected Behavior

Two new data types should be added to the kernel type system:

- A **geometry** type that carries a Spatial Reference System Identifier (SRID) in authority:code notation (e.g., EPSG:4326, OGC:CRS84). Omitting the SRID on deserialization should use a default (OGC:CRS84), but serialization should always write the full form.
- A **geography** type that carries both an SRID and a geodetic calculation algorithm. The supported algorithms are: spherical, vincenty, thomas, andoyer, and karney. Omitting either the SRID or the algorithm on deserialization should fall back to defaults (OGC:CRS84 and spherical respectively), but serialization should always write the full form.

Both types must round-trip through Delta's JSON schema representation correctly. Invalid configurations — empty parameters, unrecognized SRID formats, unknown algorithms, or too many parameters — must be rejected with clear error messages. Both types must also be usable as nested element types inside arrays and struct fields.

## Why This Matters

Without built-in spatial type support, Delta tables cannot faithfully represent geospatial datasets through the kernel API. Adding these two types enables engines and connectors to define, read, and write spatial columns with proper type metadata, opening the door to interoperability with spatial standards.
