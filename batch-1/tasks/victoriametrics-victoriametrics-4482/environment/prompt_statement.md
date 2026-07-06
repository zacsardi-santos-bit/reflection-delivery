I'm working on adding Loki-compatible log ingestion support to VictoriaLogs. I need to implement logic to handle log push requests from Loki clients in both the JSON and the binary (protobuf with snappy compression) formats.

For JSON requests, the body follows the standard Loki push format where streams carry label key-value pairs plus a list of log entries, each with a nanosecond timestamp and a message string. The handler should process each entry by invoking a callback with the entry's timestamp and its associated fields — stream labels should become individual named fields, and the log message should become a dedicated message field. The handler should return the count of processed entries and return an error for empty, malformed, or truncated input.

For the binary format, I need a similar handler that reads a snappy-compressed protobuf Loki push body and processes it through the same callback interface, returning the count of processed entries.

Additionally, I need a utility function in the log storage layer that parses a tenant identifier from a plain string. The string can be just an account number, a colon-separated pair of account and project numbers, or either part omitted. An empty string should map to a zero-value tenant identifier.
