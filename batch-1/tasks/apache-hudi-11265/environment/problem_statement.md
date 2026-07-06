## Description

Hudi's JSON-to-Avro converter does not handle several standard Avro logical types. When a Hudi table schema uses decimal, date, time, timestamp, local-timestamp, duration, or UUID column types, writing JSON records to that table either silently produces wrong data or crashes in an unhelpful way.

This gap makes it impossible to reliably ingest JSON data into Hudi tables with these column types, which are widely used in real-world schemas.

## Expected Behavior

- **Decimal** fields should accept human-readable string numbers or numeric values and produce correctly encoded Avro bytes or fixed representations. Values that exceed the schema's precision or scale should be rejected with a clear error.
- **Date** fields should accept ISO date strings (e.g., "2020-09-01"), epoch-day integers, or their string equivalents.
- **Time** fields (both millisecond and microsecond precision) should accept numeric epoch offsets or "HH:MM:SS[.fractional]" strings.
- **Timestamp** fields (UTC, both millisecond and microsecond precision) should accept ISO-8601 datetime strings with a 'Z' UTC marker or numeric epoch values.
- **Local-timestamp** fields should accept ISO-8601 local datetime strings without a timezone component, or numeric epoch values.
- **Duration** fields should accept a three-element list of integers representing months, days, and milliseconds.
- **UUID** fields should pass string values through as-is.
- For all logical types, inputs that do not conform to the schema (wrong format, out-of-range values, schema misconfiguration) should raise a clear, specific error.

## Why This Matters

Users who model their data with standard Avro logical types and want to ingest JSON data into Hudi currently have no reliable path to do so for these types. Supporting them makes Hudi usable with the full breadth of Avro-typed schemas.
