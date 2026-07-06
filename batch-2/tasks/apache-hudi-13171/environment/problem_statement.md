## Description

Hudi's file group reading infrastructure currently relies on a test-only reader context stub for Avro-based record reading. This stub does not fully implement all required behaviors — in particular, it does not correctly merge skeleton and data file iterators for bootstrap tables. This means tests for record merging strategies and bootstrap reading are not validating the actual production code paths.

We need a proper, engine-independent Avro reader context in the core library that can be used in any JVM environment. This implementation should handle:

- Extracting field values from Avro records by field name, returning null when a field is absent from the schema
- Resolving record keys either from embedded metadata fields (when the table is configured to populate them) or by delegating to a configurable key generator
- Merging two file iterators (skeleton partition file and base data file) into a single stream of combined records with the correct combined schema, preserving field ordering

## Expected Behavior

- When merging bootstrap skeleton and base file iterators, the resulting records should contain skeleton fields first, followed by base fields, using a merged schema
- Merging two equally-sized iterators should produce a result with exactly the same number of records
- Merging two empty iterators should produce an empty result
- Merging iterators of different sizes should result in an error (the iterators must be in sync)
- Field value lookup by name should return null for fields not present in the schema
- Record key extraction should use key generator delegation or metadata field lookup based on the table configuration

## Why This Matters

Without this implementation, integration tests for file group reading cannot use the real production logic and instead depend on test stubs. This prevents catching bugs in Avro-based reading across all JVM-based environments and limits reuse of the reader context across different engines.
