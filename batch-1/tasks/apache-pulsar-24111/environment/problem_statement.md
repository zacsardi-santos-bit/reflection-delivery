## Description

When Pulsar broker applies dispatch rate limiting to subscriptions, there is currently no way to know which level of rate limit (broker-level, topic-level, or subscription-level) actually caused the throttling. Operators who notice degraded message delivery rates have no way to distinguish whether the bottleneck is coming from a broker-wide configuration, a topic-level policy, or a per-subscription policy.

## Expected Behavior

- Subscription statistics exposed via the admin API should include separate counters for how many times dispatching was throttled due to broker-level limits, topic-level limits, and subscription-level limits — for both message count and byte count.
- When only one level of rate limiting is active, only the counters for that level should be non-zero; the others should remain at zero.
- Prometheus metrics should expose the same throttle event data with a label indicating which level of rate limiting caused the event (broker, topic, or subscription), allowing time-series monitoring and alerting.

## Why This Matters

Without this visibility, operators tuning rate-limiting policies have no insight into which policy tier is actually constraining dispatch. Adding these labeled counters makes it possible to immediately identify the source of dispatch throttling for any subscription, enabling faster diagnosis and more targeted configuration changes.
