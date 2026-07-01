Implement a pull operation for the log service that allows consumers to retrieve stored records starting from a specified position, up to a configurable batch size. Ensure both push and pull operations validate collection identifiers, returning clear errors for invalid inputs.

*   Implement the `PullLogs` method on `recordLogDb` in `go/coordinator/internal/metastore/db/dao/record_log.go`.
    *   Retrieve records for a given collection where the ID is greater than or equal to `startFromId`, limited to `batchSize`.
    *   Return records in ascending ID order.
    *   If `startFromId` is 0, start from the first available ID (1).

*   Implement the `PullLogs` gRPC endpoint on `Server` in `go/coordinator/internal/logservice/grpc/record_log_service.go`.
    *   Accept a `PullLogsRequest` with `CollectionId` (string), `StartFromId`, and `BatchSize`.
    *   Return a `PullLogsResponse` with a `Records` field containing the matching records.
    *   Clear the `CollectionId` field on each returned record.
    *   Validate `CollectionId`; if invalid, return a nil response and a gRPC error with status `InvalidArgument` and message "invalid collection_id".

*   Update the existing `PushLogs` method on `Server` to validate `CollectionId`.
    *   Return a nil response and a gRPC error with status `InvalidArgument` and message "invalid collection_id" when `CollectionId` is invalid.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.