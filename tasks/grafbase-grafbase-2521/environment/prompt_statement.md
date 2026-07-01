I'm working on the Grafbase gateway and I'd like it to support automatic schema hot-reloading when using a local schema file. Right now, if I start the gateway pointing at a local federated schema file and then modify that file, the running gateway continues to serve the old schema — the only way to get the new schema loaded is to restart the process entirely.

I'd like the gateway to detect changes to the schema file automatically and reload the schema without requiring a restart. When the file is saved with new content, the gateway should pick up those changes within a few seconds and serve the updated schema. After the reload, GraphQL introspection should reflect the new type definitions — so if I remove a field, for example, it should no longer show up in introspection results.

This would make local development much smoother since I wouldn't need to restart the gateway every time I iterate on the schema.
