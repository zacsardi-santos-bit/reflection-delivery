## Description

When transpiling Snowflake time-conversion functions to other SQL dialects, two related issues arise:

1. **Compact time format strings are not recognized.** When a time value is provided alongside a format string that combines hours, minutes, and seconds without any separator characters (e.g. a compact time string where all time components appear as a single run of digits, with a matching format descriptor that has no separators), the transpilation fails or produces an incorrect result. The compact format should be recognized and converted to the equivalent format used by each target dialect.

2. **The "try" variant uses strict operations instead of safe ones.** The nullable/safe variant of the time conversion function — the one that returns null instead of raising an error on bad input — is incorrectly transpiled to DuckDB using strict parsing and casting functions. It should use the error-tolerant equivalents for both the string-parsing step and the type-cast step.

## Expected Behavior

- A time conversion call with a compact format string (no separators between time components) should be correctly transpiled to DuckDB and remain valid in Snowflake, with format strings normalized to a consistent case.
- The safe/try variant of the time conversion function, when transpiled to DuckDB, should use the nullable parsing function (returning null on failure) and the nullable cast — not the strict versions.
- Time conversion functions invoked with a format string should be correctly inferred as producing a time value for type-checking purposes.
- When the input to a time conversion function is already a timestamp-typed expression, it should be wrapped or cast appropriately when converted to other dialects.

## Why This Matters

Users writing Snowflake SQL with time values in compact numeric formats, or using the safe/try variant of the time conversion function, get incorrect transpilation results when targeting DuckDB or other dialects. This can cause queries to behave differently across dialects — raising errors in situations that should silently return null, or failing to produce any output at all for valid compact time format strings.
