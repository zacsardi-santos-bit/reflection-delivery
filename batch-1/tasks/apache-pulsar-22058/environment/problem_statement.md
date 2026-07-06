## Description

The Pulsar broker needs to record duration-based metrics (e.g., latency histograms) in seconds, which is the unit expected by modern observability systems like OpenTelemetry. Currently there is no shared utility to convert a duration expressed in an arbitrary time unit (such as nanoseconds or milliseconds) into seconds while preserving fractional precision.

The standard library's integer conversion truncates sub-second values to zero — a 1-millisecond duration would become 0 seconds. This makes it impossible to accurately report short-lived operations in metrics.

## Expected Behavior

- A new utility class in the common stats package should provide a static method to convert any duration, given as a numeric amount and a time unit, into a fractional double value in seconds.
- Conversions must preserve full precision across all standard time units:
  - 1 hour → 3600.0 seconds
  - 1 minute → 60.0 seconds
  - 1 second → 1.0 seconds
  - 1 millisecond → 0.001 seconds
  - 1 microsecond → 0.000001 seconds
  - 1 nanosecond → 0.000000001 seconds

## Why This Matters

Without a precise seconds-conversion utility, duration-based metrics in the broker would lose accuracy for sub-second events, which are common in latency recording. A shared utility prevents duplication and ensures all metrics components produce consistent, accurate values.
