## Description

The Debezium JSON format deserializer in SeaTunnel does not correctly handle the full range of type encodings used by different database connectors. When processing change data capture events from MySQL, SQL Server, Oracle, and PostgreSQL, the connector-specific encoding conventions for date, time, timestamp, decimal, and binary data are not all handled properly.

Different databases encode the same logical types in different ways. For example:
- Date and time values may be encoded as integer epoch values (in days, milliseconds, microseconds, or nanoseconds), or as ISO timestamp strings with timezone suffixes — and the unit varies by connector and by column type
- Oracle encodes its decimal/numeric types as a structured object containing a base64-encoded binary representation rather than a plain number
- Binary and bit column values are base64-encoded strings that need to be decoded and then converted to the correct target type

Without correct handling of these encoding differences, data ingested from these databases will be incorrectly parsed or fail entirely.

## Expected Behavior

- MySQL: all integer, floating-point, decimal, string, binary, bit, date/time, and timestamp fields must be correctly deserialized, including epoch-based date/time values and ISO timestamp strings
- SQL Server: date/time values encoded in nanoseconds, milliseconds, and ISO strings (including datetimeoffset) must all be correctly converted to SeaTunnel temporal types with timezone information stripped
- Oracle: NUMBER and FLOAT columns encoded as base64-structured objects must be converted to decimal values; Oracle date and timestamp columns encoded in various numeric and string formats must yield correct local date-time values
- PostgreSQL: all temporal encoding variants (microseconds, milliseconds, ISO strings, time-with-timezone strings) must correctly produce the expected local time and local date-time values

## Why This Matters

SeaTunnel users ingesting data through Debezium CDC from any of these four major databases cannot rely on the data being correctly typed. This causes silent data corruption or runtime errors during pipeline execution.
