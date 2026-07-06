Implement support for Loki-compatible log ingestion in VictoriaLogs by handling JSON and binary (protobuf) push requests. Ensure correct parsing of timestamps and tenant identifiers, and handle errors appropriately.

*   Implement the `processJSONRequest` function in `app/vlinsert/loki/`:
    *   Accept an `io.Reader` containing a Loki JSON push request body and a callback function.
    *   Parse each log entry, invoking the callback with the entry's nanosecond timestamp as `int64` and associated fields as `[]logstorage.Field`.
    *   Ensure stream labels appear as individual fields and the log message is a field named "_msg".
    *   Return the count of processed entries and a nil error on success.
    *   Return a non-nil error for empty input, invalid JSON syntax, or truncated JSON.

*   Implement the `parseLokiTimestamp` function in `app/vlinsert/loki/`:
    *   Parse a nanosecond Unix timestamp string to `int64`.
    *   Return the parsed timestamp for valid inputs like "1687510468000000000".
    *   Return a non-nil error for unparseable inputs.

*   Implement the `processProtobufRequest` function in `app/vlinsert/loki/`:
    *   Accept an `io.Reader` containing a snappy-compressed protobuf Loki push request and a callback function.
    *   Process all entries, invoking the callback for each entry.
    *   Return the count of processed entries and a nil error on success.

*   Define the following structs in the `loki` package:
    *   `PushRequest` in `push_request.pb.go` or similar:
        *   Fields: `Streams []Stream`.
        *   Methods: `Marshal() ([]byte, error)`, `Unmarshal([]byte) error`.
    *   `Stream` in `types.go`:
        *   Fields: `Labels string`, `Entries []Entry`.
    *   `Entry` in `types.go`:
        *   Fields: `Timestamp time.Time`, `Line string`.

*   Implement the `GetTenantIDFromString` function in `lib/logstorage/tenant_id.go`:
    *   Parse a tenant identifier string into a `TenantID` value.
    *   Handle formats like "", "123", "123:456", "123:", ":456".
    *   Return a nil error for all valid inputs.

*   Ensure the `logstorage.Field` struct has `Name` and `Value` fields of type `string`.
*   Ensure the `TenantID` struct has `AccountID` and `ProjectID` fields of type `uint32`, and a `String()` method.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.