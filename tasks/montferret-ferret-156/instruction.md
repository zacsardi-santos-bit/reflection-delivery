Implement three functions in the ferret datetime standard library to extract the hour, minute, and second components from a date-time value. Ensure each function accepts exactly one date-time argument and returns the corresponding component as an integer. Register these functions in the datetime library for accessibility.

*   Implement the `DateHour` function:
    *   Accept exactly one argument of DateTime type.
    *   Return an integer representing the hour component (0-23).
    *   Return an error if called with zero arguments or more than one argument.
    *   Use the signature: `DateHour(_ context.Context, args ...core.Value) (core.Value, error)`.
    *   Implement in the file: `pkg/stdlib/datetime/hour.go`.

*   Implement the `DateMinute` function:
    *   Accept exactly one argument of DateTime type.
    *   Return an integer representing the minute component (0-59).
    *   Return an error if called with zero arguments or more than one argument.
    *   Use the signature: `DateMinute(_ context.Context, args ...core.Value) (core.Value, error)`.
    *   Implement in the file: `pkg/stdlib/datetime/minute.go`.

*   Implement the `DateSecond` function:
    *   Accept exactly one argument of DateTime type.
    *   Return an integer representing the second component (0-59).
    *   Return an error if called with zero arguments or more than one argument.
    *   Use the signature: `DateSecond(_ context.Context, args ...core.Value) (core.Value, error)`.
    *   Implement in the file: `pkg/stdlib/datetime/second.go`.

*   Register the functions in the datetime library:
    *   Add `DateHour` under the key "DATE_HOUR".
    *   Add `DateMinute` under the key "DATE_MINUTE".
    *   Add `DateSecond` under the key "DATE_SECOND".
    *   Update the file: `pkg/stdlib/datetime/lib.go`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.