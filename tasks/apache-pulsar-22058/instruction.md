Implement a utility class to convert durations into seconds with full precision for use in the Pulsar broker. This utility will ensure accurate reporting of sub-second events by converting various time units into a fractional double value in seconds.

*   Create a class named `MetricsUtil` in the package `org.apache.pulsar.common.stats`.
    *   File path: `pulsar-common/src/main/java/org/apache/pulsar/common/stats/MetricsUtil.java`.
*   Implement a static method `convertToSeconds` within `MetricsUtil`.
    *   Method signature: `convertToSeconds(long duration, TimeUnit timeUnit) -> double`.
    *   Accepts:
        *   `long duration`: the duration to convert.
        *   `java.util.concurrent.TimeUnit timeUnit`: the unit of the duration.
    *   Returns:
        *   A `double` representing the equivalent duration in seconds.
*   Ensure the `convertToSeconds` method:
    *   Preserves fractional precision for sub-second conversions:
        *   `convertToSeconds(1, MILLISECONDS)` must return `0.001`.
        *   `convertToSeconds(1, MICROSECONDS)` must return `0.000001`.
        *   `convertToSeconds(1, NANOSECONDS)` must return `0.000000001`.
    *   Correctly converts whole-second multiples:
        *   `convertToSeconds(1, HOURS)` must return `3600.0`.
        *   `convertToSeconds(1, MINUTES)` must return `60.0`.
        *   `convertToSeconds(1, SECONDS)` must return `1.0`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.