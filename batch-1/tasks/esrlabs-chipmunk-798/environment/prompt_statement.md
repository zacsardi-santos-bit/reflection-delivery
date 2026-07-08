I'm working on a log file indexing and timestamp parsing library in Rust and I've got a cluster of related issues I want to knock out together.

First off there's no way to sanity-check a timestamp format string before I actually throw a log file at it. I want a validation function that takes a format string and confirms it has all the required pieces (year, month, day, hour, and minute), returning a success result that carries the compiled regex pattern, or a failure result with a message describing what's missing. Seconds should count as optional, and the timezone component's optional too. Right now I only find out a format is broken when parsing a real file, which surfaces errors way too late in the workflow.

Second, the timestamp extraction function straight up crashes when the format string doesn't include a seconds field. I've got log lines shaped like year-month-day hours:minutes with no seconds at all, and those should parse fine with seconds defaulting to zero instead of blowing up.

Third, the functions that scan a file for timestamps and figure out its timespan don't take a fallback year. I want them to accept an optional year param so files whose log lines don't carry a year can still get a year hint at this API level.

And finally there's some type sloppiness where file sizes coming off filesystem metadata get cast down to a narrower integer before being handed to the indexing functions. That's unnecessary and it can bite on 32-bit platforms or with very large files. The size should stay as the native 64-bit type the whole way through, so the indexing APIs should just accept that type directly and callers shouldn't have to do any explicit conversion.
