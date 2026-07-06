## Description

The datetime standard library already supports extracting the date components (year, month, day) from a date-time value, but there is no way to extract the time-of-day components: hour, minute, and second. This is a significant gap — scripts that need to work with or compare specific time portions of a date-time value have no built-in support for doing so.

## Expected Behavior

- A function should exist to extract the **hour** component from a date-time value, returning it as an integer (0–23).
- A function should exist to extract the **minute** component from a date-time value, returning it as an integer (0–59).
- A function should exist to extract the **second** component from a date-time value, returning it as an integer (0–59).
- Each of these functions should accept exactly one date-time argument. Passing zero arguments or more than one argument should result in an error.

## Why This Matters

Without these functions, developers writing queries that involve time-of-day filtering or formatting must work around the limitation using string manipulation or other indirect approaches. Adding dedicated hour, minute, and second extraction functions brings the datetime library to feature parity with what users naturally expect from a date/time toolkit.
