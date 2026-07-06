I'm working on the Python client for a metadata catalog system, and I need to add support for managing machine learning models and their versions. Right now the client supports filesets and other resource types, but there's no catalog type for models at all — no way to register a model, track its versions, get metadata about them, or delete them.

I need a new catalog type that exposes a full model management API: listing models in a schema, getting a model by name, registering a new model with optional comments and properties, deleting a model, and similar operations for model versions — listing all versions by number, fetching a version by its number or by a human-readable alias, linking a new versioned snapshot to a model with a storage URI, and deleting versions by number or alias.

I also need the response objects for reading model data and model version data from the server — they need to deserialize from JSON and validate that required fields like the version number, URI, and audit info are present, raising appropriate errors when they're not.

Additionally, several existing modules (including the fileset catalog and the DTO converter utilities) need to be moved from their current package location into the client package, and all existing import paths for those modules need to be updated accordingly.
