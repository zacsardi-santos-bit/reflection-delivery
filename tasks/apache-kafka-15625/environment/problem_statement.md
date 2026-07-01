## Description

The Remote Log Manager in Kafka — the component responsible for copying log segments to remote storage and fetching them back — currently has no mechanism to limit how many bytes per second it reads from or writes to remote storage. This means these background operations can consume unbounded bandwidth, potentially starving other critical broker operations.

We need quota management support for both copy (upload) and fetch (download) operations in the Remote Log Manager. Each operation type should have its own independently configurable byte-rate limit, time-window size, and sample count. If no limit is configured, the default behavior should be to allow unlimited throughput (effectively no quota). Quotas should also be updatable at runtime without disturbing other internal metrics tracking.

## Expected Behavior

- A new quota configuration holder should exist for the Remote Log Manager, carrying the byte-rate limit, number of rolling-window samples, and window duration.
- A new quota manager should be able to track recorded byte usage against a configured rate limit and report whether the quota is currently exceeded.
- Exceeding the byte-rate limit within the rolling time window should be detectable; once the window rolls past the recorded activity, the quota should no longer be reported as exceeded.
- Updating the quota at runtime should affect only the quota-related rate metrics, leaving all other metrics unchanged.
- The Remote Log Manager configuration should expose separate quota settings for copy and fetch operations, with sensible unlimited defaults.
- The Remote Log Manager itself should accept the metrics infrastructure as a constructor argument so it can register and track these quota metrics.

## Why This Matters

Without rate limiting, the remote log background threads can monopolize network and storage I/O, impacting produce/consume latency on the broker. Exposing configurable quotas gives operators a knob to protect foreground traffic from background remote storage activity.
