# Support Multi-Objective Optimization in RDB Storage

## Description

Currently, the relational database storage backend stores a single optimization direction (minimize or maximize) as a field on the study record itself, which makes it impossible to support studies with multiple objectives. Similarly, trial results are stored as a single value per trial.

To enable multi-objective optimization, the schema needs to be refactored so that:
- Optimization directions are stored in a dedicated table with one row per objective per study, each row having an objective index and a direction value.
- Trial objective values are stored in a dedicated table with one row per objective per trial, each row having an objective index and the corresponding value.

## Expected Behavior

- A study can have multiple optimization directions, one per objective index, stored and retrievable independently.
- It should be possible to look up a direction by study and objective index, or retrieve all directions for a study at once.
- A trial can have multiple objective values, one per objective index, stored and retrievable independently.
- It should be possible to look up a value by trial and objective index, or retrieve all values for a trial at once.
- Deleting a study should automatically remove all its associated direction records.
- Deleting a trial should automatically remove all its associated value records (both objective values and intermediate values).
- The database schema version history must be updated to reflect this new migration.

## Why This Matters

Multi-objective optimization is a common use case — users want to simultaneously optimize for conflicting goals (e.g., minimize loss while also minimizing runtime). The current single-direction, single-value schema is a fundamental blocker for this capability. Refactoring the schema into per-objective rows with proper cascade deletion lays the groundwork for full multi-objective support.
