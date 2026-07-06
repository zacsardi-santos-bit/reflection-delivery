I'm working on a log file indexing and timestamp parsing library in Rust, and I've run into a few related issues I'd like to fix together.

First, there's no way to validate a timestamp format string before using it — I'd like a function that checks whether a format string has all the required parts (year, month, day, hour, minute) and returns either a success result containing the compiled pattern, or a failure result explaining what's missing. Seconds should be considered optional. The timezone component should also be optional.

Second, the timestamp extraction function crashes when the format string doesn't include a seconds field. I have log lines with formats like year-month-day hours:minutes (no seconds), and those should parse successfully with seconds defaulting to zero.

Third, the functions that scan a file for timestamps and determine its timespan don't accept a fallback year parameter. I'd like them to accept an optional year that can be used when the year isn't present in the log lines themselves.

Finally, there are some type inconsistencies where file sizes from filesystem metadata are being cast to a narrower integer type before being passed to indexing functions. The file size should be kept as the native 64-bit type throughout, so the indexing APIs should accept that type directly without the callers needing to do any explicit conversion.
