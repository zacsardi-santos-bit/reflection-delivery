## Description

Cortex's ingester supports limits on the number of active time series per user and per metric name, but there is currently no way to enforce series limits based on specific label combinations. This means operators cannot prevent a particular label value combination — such as a specific environment, job, or region label value — from generating an unbounded number of time series within a tenant's quota.

We need the ability to configure per-labelset series limits: operators should be able to define one or more label sets, each with its own maximum number of active series. When a write request would push the number of series matching a configured labelset over its limit, the request must be rejected with an appropriate error that identifies which constraint was violated.

## Expected Behavior

- Operators can configure one or more labelset-based series limits per tenant, where each limit specifies a set of label name-value pairs and a maximum series count.
- When a push would exceed any configured per-labelset limit, the ingester rejects it with a 400 Bad Request error that identifies the exceeded limit.
- The ingester exposes a new gauge metric tracking the number of currently active series per user and configured labelset. This metric updates as series are added or removed and is cleaned up when a limit is no longer configured.
- When per-labelset limits are added or removed at runtime, the ingester adapts accordingly — bootstrapping counts from existing data for newly added limits and removing stale metrics when limits are removed.
- Active series per labelset counts persist correctly across ingester restarts.

## Why This Matters

Multi-tenant Prometheus deployments need granular cardinality controls to prevent specific label dimensions from consuming disproportionate resources. Without per-labelset limits, operators cannot guard against cardinality explosions from specific label combinations even when global per-user limits are in place.
