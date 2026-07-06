## Description

When parsing datetime strings that include timezone offset information using the best-effort date parsing mode in ClickHouse, applying the timezone adjustment can push the resulting UTC timestamp outside the valid range for the standard 32-bit date-time type. This boundary violation is not currently detected, so values that belong to the wider-range high-precision date-time type end up silently misrepresented: timestamps just above the upper limit wrap around to incorrect dates, and timestamps just below the lower limit are clamped, both resulting in data loss.

## Expected Behavior

- A datetime string whose timezone offset pushes the UTC time **above** the upper boundary of the standard date-time type should be automatically inferred as the wider-range, high-precision date-time type — preserving the correct UTC value.
- A datetime string whose timezone offset pushes the UTC time **below** the epoch (i.e., to a negative timestamp) should similarly be inferred as the wider-range, high-precision date-time type — not clamped to zero.
- Datetime strings whose timezone-adjusted UTC timestamps stay within the valid range for the standard date-time type should continue to be inferred as that type, unchanged.

## Why This Matters

Users storing timestamps near the boundary of the supported date range with non-UTC timezone offsets get silently wrong values. For example, a timestamp that is technically within range in a given timezone but shifts out of range once converted to UTC will be stored incorrectly rather than being automatically upgraded to the appropriate wider-range type. This is a correctness issue that can lead to subtle data corruption for edge-case but valid timestamps.
