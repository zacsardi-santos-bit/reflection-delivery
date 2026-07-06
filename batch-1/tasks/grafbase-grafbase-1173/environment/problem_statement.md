## Description

We need a new library that can compare two GraphQL schemas and produce a structured list of differences between them. Currently there is no way to programmatically detect what changed between two versions of a schema — for example, whether a type was added or removed, whether a field's type changed, whether a directive definition was dropped, or whether an argument gained or lost a default value.

## Expected Behavior

- Given any two valid GraphQL schema strings (a "source" and a "target"), the library should return a sorted list of changes
- Each change should identify what kind of modification occurred and which schema element was affected (using a dot-separated path)
- Supported change categories should include: object type additions and removals, field additions and removals, field type changes, interface implementation changes, enum additions/removals/value changes, union additions/removals/member changes, directive definition changes, schema-level root type changes, field argument additions/removals/type changes, and argument default value additions/removals/changes
- The diff must work bidirectionally — callers should be able to compare schemas in both directions
- When a type changes its fundamental kind (e.g., an object becomes an enum), both the removal of the old kind and the addition of the new kind should be reported

## Why This Matters

This capability is essential for tracking schema evolution over time, detecting breaking changes before deployment, and generating changelogs or migration guidance when a GraphQL API evolves. Without it, teams must manually inspect schema diffs to understand what changed and what might be affected downstream.
