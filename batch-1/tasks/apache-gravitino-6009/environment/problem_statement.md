## Description

The Python client library for this catalog system currently has no support for managing machine learning models and their versioned artifacts. Developers who want to register a model, track its versions, retrieve metadata, or remove outdated snapshots have no API to do so through the Python client. There is also no catalog type dedicated to model management, so the client cannot even load a model-focused catalog from the server.

In addition, several existing catalog-related modules are located under a package that mixes different concerns. Moving these modules to a more focused client package would improve organization and make the codebase easier to navigate.

## Expected Behavior

- A new catalog type for models should be supported, including the ability to load it from a catalog.
- The client should expose operations to: list registered models in a schema, retrieve a model by identifier, register a new model with optional comment and properties, and delete a model.
- For model versions, the client should support: listing all version numbers for a model, retrieving a specific version by number, retrieving a specific version by alias, linking a new version with a URI and optional aliases, deleting a version by number, and deleting a version by alias.
- Response types for model and model version data should be deserializable from server responses, with proper validation that raises errors when required fields are absent.
- Existing catalog-related modules should be importable from their new package locations within the client package.

## Why This Matters

Without this support, teams using the Python client cannot manage ML model lifecycle through the catalog system at all. Models represent a distinct resource type alongside tables, filesets, and topics — they deserve first-class support in the client library with the same level of API completeness.
