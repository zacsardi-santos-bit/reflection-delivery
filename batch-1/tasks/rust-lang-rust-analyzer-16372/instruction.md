Implement support for a new import style in rust-analyzer where all imports are collected into a single top-level braces block. Update the tool to detect and insert imports in this style, ensuring that new imports are merged appropriately based on visibility and attributes.

*   Update the `ImportGranularity` enum in `crates/ide-db/src/imports/insert_use.rs`:
    *   Add a new variant `One` to represent the single top-level braced use statement style.

*   Update the `MergeBehavior` enum in `crates/ide-db/src/imports/merge_imports.rs`:
    *   Add a new variant `One` to drive the merging logic for the one-style import.

*   Implement import insertion logic:
    *   When `ImportGranularity::One` is active, wrap new imports in a root-level brace group.
    *   Merge new imports with existing imports if they share the same visibility and attributes.
    *   Insert new imports as separate statements if they have different visibility or attributes.

*   Update granularity detection logic:
    *   Add a new variant `One` to the `ImportGranularityGuess` enum in `crates/ide-db/src/imports/insert_use.rs`.
    *   Return `ImportGranularityGuess::One` when a scope contains a single top-level braced use statement.
    *   Return `ImportGranularityGuess::One` when multiple one-style use statements have different visibility or attributes.
    *   Return `ImportGranularityGuess::Unknown` when multiple one-style use statements share the same visibility or attributes.

*   Add test configurations and helper functions:
    *   Define `TEST_CONFIG_IMPORT_ONE` in `crates/ide-assists/src/tests.rs` with `ImportGranularity::One` and `enforce_granularity` set to true.
    *   Implement `check_assist_import_one` to test assists using the one-style configuration.
    *   Implement `check_assist_not_applicable_for_import_one` to verify assists are not applicable under the one-style configuration.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.