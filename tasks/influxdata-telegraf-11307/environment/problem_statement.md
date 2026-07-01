## Description

Telegraf currently has no built-in way to serialize collected metrics into CSV (comma-separated values) format. Many data pipelines, log aggregation systems, and storage backends expect tabular output, but users who need CSV must post-process Telegraf's output themselves. A native CSV serializer would let Telegraf write metrics directly into this format without external tooling.

## Expected Behavior

- A new CSV output serializer that converts each metric into a row of delimited values: timestamp, measurement name, tag values (in tag order), and field values (sorted alphabetically by field name).
- Configurable timestamp format: Unix epoch seconds by default, with options for milliseconds, microseconds, nanoseconds, or any formatted date/time string layout.
- Configurable column delimiter: comma by default, but any single character (e.g. semicolon) should be supported. Multi-character separators must be rejected at configuration time with a clear error message.
- Optional header row: when enabled, a header line naming each column is written once at the start of the output, then suppressed for subsequent rows.
- Optional column type prefix: when enabled, tag column names in the header are prefixed to indicate they are tags, and field column names are prefixed to indicate they are fields.
- Unrecognized timestamp format strings must be rejected at configuration time with a clear error message.

## Why This Matters

Without a CSV serializer, users sending metrics to CSV-based destinations must add an extra processing step outside of Telegraf. A native serializer makes it straightforward to write properly formatted tabular output — with configurable headers, delimiters, and timestamp styles — directly from Telegraf's output plugins.
