## Description

When multiple router instances are deployed, they all poll the control plane for configuration updates at the same fixed interval. This causes them to fire requests simultaneously, creating periodic burst load on the configuration server — a classic thundering herd problem.

We need to add a configurable jitter value to the polling mechanism so that each instance can offset its poll by a small random delay, spreading the requests over time. The polling component should accept this jitter maximum alongside the existing poll interval.

## Expected Behavior

- The poller should accept a maximum jitter duration in addition to the poll interval.
- When created with invalid parameters — a zero or negative poll interval, or a negative jitter maximum — the poller should fail immediately rather than silently producing incorrect behavior at runtime.
- A jitter maximum of zero is valid (it simply means no jitter is added).
- The random delay generation utility should return a random value within the specified maximum when the maximum is positive, return zero when the maximum is zero, and fail immediately for negative maximums.

## Why This Matters

Without jitter, all router instances synchronize their polling cycles, potentially overloading the configuration backend at regular intervals. Adding even a small random offset per instance significantly smooths out the request distribution and improves system stability under load.
