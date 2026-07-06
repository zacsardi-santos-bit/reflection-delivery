Implement a unified name-lookup capability in the key-value service to resolve resource names by type and ID. Update test helper functions to return the store interface type for flexibility in testing.

*   Implement the `Name` method in `kv/lookup_service.go` with the following signature:
    *   `func (s *Service) Name(ctx context.Context, resource influxdb.ResourceType, id influxdb.ID) (string, error)`
    *   Ensure it resolves the human-readable name of a resource given its type and ID.
    *   Return an empty string (no error) for resource types without meaningful names (authorizations, tasks).
    *   Return an error if the ID is invalid, the resource type is unrecognized, or if no resource with the given ID exists for types that support names.
*   Ensure the `Name` method handles specific resource types:
    *   For `bucket`, `dashboard`, `organization`, `source`, `telegraf`, and `user` resource types:
        *   Look up the resource by ID and return its `Name` field.
        *   Return an error if no resource with that ID exists.
    *   For `authorization` and `task` resource types:
        *   Return an empty string and no error.
*   Update test helper functions in `kv/kv_test.go`:
    *   Modify `NewTestBoltStore` to return `kv.Store` interface, a cleanup function, and any error:
        *   `func NewTestBoltStore() (kv.Store, func(), error)`
    *   Modify `NewTestInmemStore` to return `kv.Store` interface, a cleanup function, and any error:
        *   `func NewTestInmemStore() (kv.Store, func(), error)`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.