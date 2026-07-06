Implement a batch authorization check feature in the API server to handle multiple authorization checks in a single request. Ensure that each check item has a unique identifier and validate the batch for constraints such as non-empty lists and maximum size limits. Map individual check errors to specific error codes and integrate the batch operation with the existing authorization layer.

*   Authorization Routing:
    *   Map the 'BatchCheck' API method to the same permission relation as the regular 'Check' operation (CanCallCheck).

*   Server Configuration:
    *   Define `DefaultMaxChecksPerBatchCheck` and `DefaultMaxConcurrentChecksPerBatchCheck` constants with a value of 50 in `internal/server/config/config.go`.

*   Batch Check Command:
    *   Accept a list of check items and execute a check resolver call for each.
    *   Return a result map keyed by `CorrelationID` and a `BatchCheckMetadata` object with `DatastoreQueryCount`.
    *   Return a `*BatchCheckValidationError` if:
        *   The checks list is empty.
        *   The number of checks exceeds the configured maximum.
        *   Any check item has an empty correlation ID (error message: 'received empty correlation id for tuple').
        *   Any correlation ID appears more than once (error message includes the duplicate ID).

*   Error Handling:
    *   When checks exceed the maximum, use the error message format: 'batchCheck received %d checks, the maximum allowed is %d'.
    *   If the request context is cancelled, return no error from `Execute`, but each `BatchCheckOutcome` should have `Err` set to `context.Canceled` and `CheckResponse` set to nil.

*   Server Handler:
    *   Validate incoming proto requests in `BatchCheck`.
    *   Return errors for empty correlation IDs ('invalid BatchCheckItem.CorrelationId') and empty checks lists ('invalid BatchCheckRequest.Checks').
    *   Use the latest authorization model if none is specified.

*   Error Mapping:
    *   Implement `transformCheckCommandErrorToBatchCheckError` to map errors to `CheckError` proto values.
    *   Implement `transformCheckResultToProto` to convert internal results to proto response format.

*   Functional Options:
    *   Implement `WithBatchCheckMaxChecksPerBatch` and `WithBatchCheckCommandCacheController` for configuring batch limits and cache controller.

*   Type Definitions:
    *   Define `CorrelationID`, `BatchCheckOutcome`, `BatchCheckCommandParams`, `BatchCheckMetadata`, and `BatchCheckValidationError` in `pkg/server/commands`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.