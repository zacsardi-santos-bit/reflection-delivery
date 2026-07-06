I'm trying to get better visibility into reader connections on my streaming server through the metrics endpoint.

*   The metrics endpoint must emit the paths_readers metric in the primary '# Paths' section, positioned after the paths metric and before paths_inbound_bytes, with three labels appearing in alphabetical order: name, readerType, and state.

*   When a path has readers, one paths_readers metric line must be emitted per unique reader type present. Each line's count must reflect the number of readers of that type (e.g., if two readers are of the same type, the count is 2). When multiple reader types are present, the metric lines must be emitted sorted alphabetically by readerType value.

*   The readerType label value must be the string representation of the connection type as defined in the API path reader type constants (for example: 'rtmpConn' for RTMP connections, 'rtspSession' for RTSP sessions).

*   The paths_readers metric must no longer appear in the '# Paths (deprecated)' section. The deprecated section must only contain paths_bytes_received and paths_bytes_sent.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.