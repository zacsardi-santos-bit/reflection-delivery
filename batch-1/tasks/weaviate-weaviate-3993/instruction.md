Implement improvements to the distributed schema store component by renaming the interface for index-level operations and enhancing test coverage for the command processor. Ensure the interface name reflects its role in handling physical index operations and update all references accordingly. Define structured responses for the command processor and handle error conditions consistently.

*   Rename the interface for index-level operations:
    *   Change the interface name from `DB` to `Indexer` in `cloud/store/store.go`.
    *   Update all references in the codebase from `store.DB` to `store.Indexer`.
    *   Ensure the `Config` struct's `DB` field is of type `Indexer`.
    *   Update the `New` function to accept a `Config` with the updated `DB` field type.

*   Implement structured responses and error handling in the command processor:
    *   Ensure `Store.Apply` always returns a value type-assertable to the `Response` struct.
    *   For non-command Raft log entries, `Apply` must return an empty `Response{}` with no error.
    *   `Apply` must panic for Raft log entries with empty or missing `Data` fields that fail proto unmarshalling.
    *   `Apply` must panic for unknown command types to signal an application upgrade is needed.
    *   Define package-level error sentinel variables `errBadRequest` and `errSchema` for error wrapping.

*   Define specific behaviors for command types in `Store.Apply`:
    *   For `add-class` command:
        *   Return a `Response` with an error wrapping `errBadRequest` for deserialization failures or nil sharding state.
        *   Return a `Response` with an error wrapping `errSchema` if the class already exists.
        *   On success, return `Response{Error: nil}` and update the in-memory schema.
    *   For `update-class` command:
        *   Return a `Response` with an error wrapping `errBadRequest` for deserialization failures.
        *   Return a `Response` with an error wrapping `errSchema` if the class does not exist.
        *   On success, return `Response{Error: nil}`.
    *   For `delete-class` command:
        *   Return `Response{Error: nil}` and remove the class from the in-memory schema.
    *   For `add-property` command:
        *   Return a `Response` with an error wrapping `errBadRequest` for deserialization failures.
        *   Return a `Response` with an error wrapping `errSchema` if the class does not exist.
        *   On success, return `Response{Error: nil}` and update the class properties.
    *   For `update-shard-status` command:
        *   Return a `Response` with an error wrapping `errBadRequest` for deserialization failures.
        *   On success, return `Response{Error: nil}`.
    *   For `add-tenant` command:
        *   Return a `Response` with an error wrapping `errBadRequest` for deserialization failures.
        *   Return a `Response` with an error wrapping `errSchema` if the class does not exist.
        *   On success, return `Response{Error: nil}` and update the tenant list.
    *   For `update-tenant` command:
        *   Return a `Response` with an error wrapping `errBadRequest` for deserialization failures.
        *   Return a `Response` with an error wrapping `errSchema` if the class or tenant does not exist.
        *   On success, return `Response{Error: nil}` and update tenant statuses.
    *   For `delete-tenant` command:
        *   Return a `Response` with an error wrapping `errBadRequest` for deserialization failures.
        *   Return a `Response` with an error wrapping `errSchema` if the class does not exist.
        *   On success, return `Response{Error: nil}` and remove the tenants.

*   Define and implement the `localDB` struct in `cloud/store/db.go`:
    *   Include an exported `Schema` field of type `*schema`.
    *   Include an unexported `store` field of type `Indexer`.
    *   Include an unexported `parser` field of type `Parser`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.