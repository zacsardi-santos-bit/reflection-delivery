## Update conda typing library dependencies to support extras

### Description

The project relies on an external family of conda typing libraries for working with package specifications and package records. These libraries have released a new set of versions that add support for "extras" — optional feature groups that users can select when depending on a package — as a field on match specifications. The new versions also add a field for extra-specific dependencies on package records.

To adopt the new library versions, all package specification types that hold large structs in enum variants must be updated to use heap-allocated wrappers to reduce stack size. Additionally, every place in the codebase that constructs a match specification must be updated to supply a value for the newly required optional-feature-groups field.

### Expected Behavior

- The project compiles successfully against the newer versions of the conda typing libraries.
- Package specification enum variants that previously stored a detailed spec inline now store it through a heap allocation.
- Converting a detailed version spec to a nameless match spec correctly populates all fields, including the new field for optional feature groups.
- TOML-based package specs can still be parsed and round-tripped correctly after the update.
- Integration test helpers for building package records also correctly initialize the new field for extra-specific package dependencies.

### Why This Matters

Without this update, the project fails to compile when the new library versions are present, preventing all tests in the package specification module from running. The boxing change also reduces stack pressure for large enum variants, which is a worthwhile improvement independently.
