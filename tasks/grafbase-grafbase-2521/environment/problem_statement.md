## Description

When running the gateway with a local federated schema file, any modifications to that file are completely ignored at runtime. If a developer changes the schema, they must restart the gateway process to pick up the new configuration. This is disruptive during development and makes iterating on schema changes unnecessarily tedious.

## Expected Behavior

- When the gateway is started with a local schema file, it should watch that file in the background for any modifications.
- When a change to the schema file is detected, the gateway should automatically reload its schema configuration without requiring a restart.
- After reloading, GraphQL introspection should immediately reflect the updated schema — including added, removed, or changed types and fields.
- The detection and reload should happen quickly enough that developers see schema changes reflected within a few seconds of saving the file.

## Why This Matters

Developers working on federated graph configurations need to iterate rapidly on their schema. Having to manually restart the gateway every time a schema change is made significantly slows down the development workflow. Automatic hot-reloading brings the local development experience in line with what developers expect from modern tooling.
