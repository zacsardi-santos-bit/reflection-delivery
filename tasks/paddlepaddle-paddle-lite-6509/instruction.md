Implement a reusable timing utility class in the Paddle-Lite framework to standardize timing operations across the codebase. This utility should accurately measure elapsed time, support controlled delays, and provide statistical summaries of timing data.

*   Create a Timer class in the `paddle::lite` namespace.
    *   Declare the class in `lite/utils/timer.h`.
    *   Implement the class in `lite/utils/timer.cc`.
*   Implement the following methods:
    *   `Timer(const std::string timer_info = "")`: Constructor with an optional label.
    *   `void Start()`: Begin measuring elapsed time.
    *   `float Stop()`: Stop timing, return the elapsed time in milliseconds as a float, and update min/max/avg statistics.
    *   `void SleepInMs(float ms)`: Pause execution for approximately the specified number of milliseconds.
    *   `void Print()`: Log the accumulated minimum, maximum, and average elapsed times in milliseconds.
*   Ensure timing accuracy:
    *   The elapsed time returned by `Stop()` must be within 10% of the actual wall-clock time as measured by `gettimeofday`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.