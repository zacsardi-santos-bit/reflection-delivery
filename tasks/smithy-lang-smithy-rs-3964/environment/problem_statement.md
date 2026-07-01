## Description

There is a bug in the smithy-rs client runtime where the retry token bucket is not actually being enforced for standard (and adaptive) retry modes. The token bucket is supposed to act as a global rate limiter on retries: as operations fail and retry, they consume tokens from a shared bucket, and once the bucket is exhausted, no further retries are allowed even if the configured maximum attempt count has not been reached. This prevents retry storms from amplifying load on an already-struggling service.

The problem is that the token bucket is not being placed into the operation's configuration before the retry strategy runs, so the retry strategy treats it as absent and skips bucket enforcement entirely. As a result, clients retry up to their full configured maximum, ignoring the token quota completely.

Additionally, token buckets are supposed to be shared across all operations targeting the same service (partitioned by service name). This partitioning is also not working: each invocation gets its own bucket instead of drawing from a shared pool. This means repeated failures from one operation do not deplete the quota seen by other operations on the same client/service, defeating the purpose of the token bucket.

## Expected Behavior

- When an operation runs with standard retry mode, a token bucket must be present in the configuration bag so the retry strategy can use it.
- The token bucket for a given service must be shared across all operations targeting that service, so that retry quota is consumed from and contributed to the same pool.
- When the shared token bucket is exhausted, retries must stop even if the maximum attempt count has not been reached.
- Operations targeting different services must use separate, independent token buckets.

## Why This Matters

Without this fix, the retry token bucket has no effect for standard retry mode, allowing unconstrained retry storms that can make outages worse. The bug is tracked in aws-sdk-rust#1234.
