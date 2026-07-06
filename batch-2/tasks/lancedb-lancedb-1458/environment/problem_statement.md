## Description

The lancedb Node.js library currently only works reliably with a narrow range of Arrow versions. Users whose projects depend on older or newer Arrow versions hit type incompatibilities or runtime errors when trying to create tables, define embedding functions, or work with schemas — even though the underlying data model is identical across Arrow versions.

## Expected Behavior

The library should support multiple major Arrow versions, so that all core operations work correctly regardless of which Arrow version the user's project uses:

- Table creation and conversion utilities should accept schemas and field types from any supported Arrow version
- Embedding function definitions should work with data types from any supported Arrow version  
- Schema-building utilities should accept field types from any supported Arrow version
- Standard table operations (adding data, querying, updating, creating indexes) should continue to work when schemas are constructed using any supported Arrow version

## Why This Matters

Teams working in larger codebases often cannot change their Arrow version due to other dependencies. If lancedb requires a specific Arrow version that conflicts with the rest of the stack, users are forced to choose between lancedb and their existing tooling. Expanding the officially supported version range removes this friction and makes lancedb usable in a wider range of real-world projects.
