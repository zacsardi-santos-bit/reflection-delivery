## Description

The Prometheus metric provider in Argo Rollouts only supports instant (point-in-time) queries. This makes it impossible to run analysis based on time series data over a configurable window — users who need to evaluate metric behavior across a range of time (not just at the current moment) have no way to express that in their analysis configuration.

## Expected Behavior

- Users should be able to configure a Prometheus metric query with a start time, end time, and step interval to perform a range query instead of an instant query.
- The start and end time boundaries should support flexible time expressions that can compute dynamic values — for example, "one hour before a specific date" — rather than requiring hardcoded timestamps.
- The result of a range query should be a flat collection of all metric values across all returned time series, which can then be evaluated using the standard success/failure conditions.
- If the start time, end time, or step interval expressions cannot be parsed, the analysis run should produce a clear error message indicating which parameter failed and why, rather than silently failing or panicking.

## Why This Matters

Many meaningful metric evaluations (such as error rate or latency patterns) are best understood over a window of time rather than at a single moment. Without range query support, users are forced to structure their analysis differently or use a more limited instant snapshot, which may not capture the data they need for a reliable rollout decision.
