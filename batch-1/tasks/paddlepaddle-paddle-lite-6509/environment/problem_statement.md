## Description

The codebase currently lacks a reusable timing utility. Several modules duplicate the same platform-specific timing boilerplate to measure elapsed time, making the code harder to maintain and inconsistent across platforms. We need a standard timing utility that any part of the framework can use to measure how long operations take.

## Expected Behavior

- A new timing utility class should be available in the utilities directory.
- Developers should be able to start a timer, run code, and stop the timer to get the elapsed milliseconds.
- The utility should support controlled delays (sleeping for a specified number of milliseconds), useful for testing and benchmarking.
- After one or more timed intervals, calling a print method should display the minimum, maximum, and average elapsed times across all measurements.
- Elapsed time measurements should be accurate to within 10% of the true wall-clock time.

## Why This Matters

Multiple files in the codebase currently each define their own local time-retrieval helpers and import platform-specific time headers. Centralizing this in one shared utility eliminates code duplication, improves cross-platform compatibility, and gives all profiling code consistent behavior.
