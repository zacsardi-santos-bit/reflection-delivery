Implement a CSV output serializer for Telegraf that converts metrics into CSV format. Ensure the serializer supports configurable timestamp formats, column delimiters, and optional header rows with column prefixes. Validate input parameters and handle errors appropriately.

*   Implement the `NewSerializer` function in `plugins/serializers/csv/csv.go`:
    *   Validate the `separator` parameter:
        *   If longer than one character, return an error: `invalid separator "X"`.
    *   Validate the `timestampFormat` parameter:
        *   If not empty, not one of "unix", "unix_ms", "unix_us", "unix_ns", or not a valid Go time layout, return an error: `invalid timestamp format "X"`.
    *   Accept empty `separator` (default to comma) and `timestampFormat` (default to "unix") without error.

*   Define the `Serializer` struct with the following fields and TOML tags:
    *   `TimestampFormat string  `toml:"csv_timestamp_format"`
    *   `Separator       string  `toml:"csv_separator"`
    *   `Header          bool    `toml:"csv_header"`
    *   `Prefix          bool    `toml:"csv_column_prefix"`

*   Implement the `Serialize` method in `plugins/serializers/csv/csv.go`:
    *   Serialize a single metric to CSV format.
    *   If `Header` is true, write a header row first and set `Header` to false.
    *   Ensure data rows follow the order: timestamp, measurement name, tag values, field values.
    *   Wrap CSV write errors with "writing data failed: ".

*   Implement the `SerializeBatch` method in `plugins/serializers/csv/csv.go`:
    *   Serialize a slice of metrics to CSV format.
    *   If `Header` is true, write one header row at the beginning.
    *   Return `(nil, nil)` for an empty slice.

*   Ensure timestamp formatting supports:
    *   "unix", "unix_ms", "unix_us", "unix_ns", and valid Go time layout strings.

*   Create test case data files in `plugins/serializers/csv/testcases/`:
    *   `basic.conf / basic.csv` — default config.
    *   `nanoseconds.conf / nanoseconds.csv` — `csv_timestamp_format = "unix_ns"`.
    *   `header.conf / header.csv` — `csv_header = true`.
    *   `prefix.conf / prefix.csv` — `csv_header = true`, `csv_column_prefix = true`.
    *   `rfc3339.conf / rfc3339.csv` — `csv_timestamp_format = "2006-01-02T15:04:05Z07:00"`, `csv_header = true`.
    *   `semicolon.conf / semicolon.csv` — `csv_separator = ";"`, `csv_header = true`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.