## Description

When the dispatcher routes requests to secondary clusters, it introduces a delay before using the primary cluster — giving the secondary a head start. This delay is currently driven entirely by observed latency statistics, with no way for operators to impose an upper bound on a per-secondary basis.

The problem is that certain secondary cluster configurations may benefit from a hard cap on this delay. For example, an operator may want to ensure that the primary is never held back for more than a few milliseconds for a particular secondary, or may want to disable the delay entirely for a given secondary cluster (by setting the cap to zero). Without a per-secondary maximum, the only available controls are global, and there is no way to disable the delay for specific secondary configurations.

## Expected Behavior

- Each secondary cluster configuration should support a maximum hedging delay field that caps how long the primary is delayed.
- When this maximum is set to zero, the primary dispatcher should not be delayed at all — even on the very first request, which would otherwise incur the default starting delay.
- The primary hedging sleep mechanism should be extracted into a self-contained component that supports cancellation: an in-progress delay can be cut short via an explicit cancel call, context cancellation, or context deadline expiration.
- Calling the cancel function before a sleep starts should have no effect on that upcoming sleep — it only applies to already-running sleeps.
- The sleep component must be safe to use from multiple concurrent goroutines.

## Why This Matters

Without a configurable maximum, the hedging delay can grow unbounded by observed latency data, and there is no way to completely suppress it for a specific secondary cluster. This makes it harder to tune and control dispatch behavior precisely in production environments.
