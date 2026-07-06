Implement a gRPC-based write service for OpenGemini to handle batch writes of binary-encoded time-series records. Ensure the service supports partial success responses, optional authentication, and health-check pings.

Requirements:

* Implement the `Service` struct in `services/writer/service.go`:
    * Create via `NewService(c config.RecordWriteConfig) (*Service, error)`.
        * Return a non-nil error if construction fails.
    * Start the gRPC server with `Open()`:
        * Listen on the address specified in `RecordWriteConfig`, default to `127.0.0.1:8305`.
    * Stop the gRPC server cleanly with `Close()`.
    * Inject a `PointWriter` using `WithWriter(w PointWriter)`.
    * Inject an `Authorizer` using `WithAuthorizer(a Authorizer)`.

* Implement the `PointWriter` interface in `services/writer/service.go`:
    * Declare `RetryWritePointRows(database string, retentionPolicy string, rows []influx.Row) error`.

* Implement the `Authorizer` interface in `services/writer/service.go`:
    * Declare `Authenticate(username string, password string, database string) error`.

* Implement the gRPC WriteService server interface:
    * Write endpoint:
        * Return `ResponseCode_Success` if all records are valid and written.
        * Return `ResponseCode_Failed` if all records are invalid.
        * Return `ResponseCode_Partial` if the batch contains a mix of valid and invalid records; write valid records.
    * Ping endpoint:
        * Return `ServerStatus_Up` in the response.

* Handle authentication:
    * If `RecordWriteConfig.AuthEnabled` is true, use `Authorizer.Authenticate` to validate write requests.
        * Reject requests with invalid credentials or insufficient privileges.
    * Accept write requests without authentication if `AuthEnabled` is false.

* Implement `RecordWriteConfig` in `lib/config/recordwrite.go`:
    * Fields:
        * `AuthEnabled` (default false) to control authentication enforcement.
        * `RPCAddress` (default "127.0.0.1:8305") for the gRPC server listen address.
    * Implement `NewRecordWriteConfig()` to return a default `RecordWriteConfig` with `AuthEnabled=false` and the default address.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.