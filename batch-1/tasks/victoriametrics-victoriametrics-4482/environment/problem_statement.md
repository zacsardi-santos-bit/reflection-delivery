## Description

VictoriaMetrics Logs (VictoriaLogs) needs support for ingesting log data via the Loki-compatible push API. Currently, the system cannot accept log push requests from Loki clients or Loki-compatible agents. We need to implement handlers for both the JSON and binary (protobuf) variants of the Loki push protocol.

## Expected Behavior

- When a Loki JSON push request is received, the system should parse all streams, extract stream labels as individual named fields, and store each log line as a message field.
- When a Loki binary (protobuf with snappy compression) push request is received, it should be handled similarly to the JSON format.
- The system should correctly parse the nanosecond-precision timestamps that Loki uses.
- Invalid or malformed push request bodies should result in an appropriate error rather than silent failure.
- Tenant identification should support parsing from a simple string notation where an account ID and project ID are separated by a colon, including partial forms where either component is omitted or empty.

## Why This Matters

Without this support, users cannot send logs to VictoriaLogs from standard Loki-compatible log collection agents such as Promtail or Grafana Agent. By implementing Loki-compatible ingestion, users can point existing Loki clients at VictoriaLogs without needing to change their log shipping configuration. The tenant string parsing is needed to correctly route logs to the appropriate tenant from HTTP request headers or query parameters.
