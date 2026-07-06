## Description

When querying tables that use second-precision datetime columns (such as those from databases that store datetimes at second-level precision), the results display raw epoch numbers instead of human-readable timestamps. This happens because the internal scalar conversion code does not have a handler for timestamps stored in second-level precision — they fall through to a generic fallback rather than being properly converted into a timestamp value.

## Expected Behavior

- Second-precision timestamp values should be converted to proper date/time representations during scalar conversion, just like microsecond- and nanosecond-precision timestamps already are.
- Queries over tables with second-precision datetime columns should return readable timestamps, not raw numeric values.

## Why This Matters

Without this fix, any data source that represents datetime values in second-level precision (which is a common and valid precision level) produces incorrect or unreadable query output. Users end up seeing epoch numbers where they expect formatted timestamps, making the data essentially unusable without manual post-processing.
