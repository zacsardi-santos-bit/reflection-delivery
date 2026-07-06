Implement a feature in the Grafbase gateway to support automatic schema hot-reloading when using a local schema file. Ensure that any modifications to the schema file are detected and applied without requiring a restart of the gateway process.

*   Update the `GraphFetchMethod::FromSchema` variant in `crates/federated-server/src/server/graph_fetch_method.rs`:
    *   Add a `schema_path: PathBuf` field alongside the existing `federated_sdl: String` field.
*   Modify the `GraphFetchMethod::into_sdl_stream` method in `crates/federated-server/src/server/graph_fetch_method.rs`:
    *   Ensure it sends the initial SDL through a channel immediately.
    *   Spawn a background task that monitors the schema file for changes and sends updated SDL through the channel when a change is detected.
*   Update the gateway argument-handling code:
    *   In `gateway/src/args/std.rs` and `gateway/src/args/lambda.rs`, populate both `federated_sdl` and `schema_path` fields when constructing `GraphFetchMethod::FromSchema`.
    *   Use the path of the schema file that was read to populate the `schema_path`.
*   Ensure the gateway detects schema file modifications and reloads the schema within approximately 5 seconds.
*   After reloading, ensure GraphQL introspection reflects the updated schema structure.
*   Create a test schema file `gateway/tests/schemas/tiny.graphql`:
    *   Define a `User` type with fields `id: ID!` and `username: String!`.
    *   Define a `Query` type with field `me: User!`.
    *   Include necessary federation directives.
*   Create a modified test schema file `gateway/tests/schemas/tiny_modified.graphql`:
    *   Define a `User` type with only the field `id: ID!` (remove `username`).
    *   Define a `Query` type with field `me: User!`.
    *   Include necessary federation directives.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.