## Description

There are two related issues with the Redis integration in Argo CD's caching layer:

1. **Fragile reconnect detection**: The hook that triggers a reconnect when Redis becomes unreachable detects connection failures by searching for a specific substring in the error message text. This approach is brittle — it can fail to detect DNS-related connection errors if the error wording changes, and it only fires after a full round-trip rather than at the connection level where the failure actually occurs.

2. **Non-functional metrics collection**: The hook responsible for recording Redis request metrics (success/failure counts and durations) is essentially broken — it does not actually count requests or record durations. As a result, the Redis performance metrics shown in the Argo CD metrics endpoint are always empty, making it impossible to observe Redis latency or error rates.

## Expected Behavior

- When a Redis operation fails because the host cannot be resolved via DNS, the reconnect callback should be reliably invoked.
- When a Redis operation succeeds, the reconnect callback should not be triggered.
- There should be a way to attach a working metrics hook to a Redis client so that every operation increments the appropriate success or failure counter and records the request duration.
- Each Redis request should count toward the duration histogram, regardless of whether it succeeds or fails.

## Why This Matters

Without reliable reconnect detection, Argo CD may silently fail to reconnect to Redis after a DNS change or restart. Without working metrics, operators have no visibility into Redis health or performance.
