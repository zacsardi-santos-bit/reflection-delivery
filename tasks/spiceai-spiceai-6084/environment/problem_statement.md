## Description

The Snowflake data connector fails when a Snowflake timestamp column is returned as a plain integer value (seconds since epoch) rather than the expected structured type containing epoch and fractional parts. This causes queries to fail instead of returning data.

## Expected Behavior

- When Snowflake returns a timestamp column as a plain 64-bit integer array (epoch seconds), the connector should successfully convert those values to Arrow's nanosecond timestamp format.
- The conversion should multiply the epoch-second value by 1,000,000,000 to produce the correct nanosecond representation (e.g., an epoch of 1,696,164,330 seconds becomes 1,696,164,330,000,000,000 nanoseconds).
- Both timezone-aware and non-timezone-aware timestamp modes should work correctly with this integer input format.
- The existing structured-type timestamp handling should continue to work as before.
- If an input is neither the known structured format nor a plain integer array, an appropriate error should be returned.

## Why This Matters

Some Snowflake configurations or query types return timestamps as bare integers rather than structured objects. Without this support, users querying such columns through the connector receive failures instead of results. Adding integer-format support makes the connector robust to both representations Snowflake may use for timestamp data.
