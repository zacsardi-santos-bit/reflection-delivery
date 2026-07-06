## Description

Currently, all runtime observability metrics—including very basic ones like the number of worker threads and how many tasks are currently alive—are gated behind an unstable feature flag. This means that even simple, non-experimental metrics cannot be used in stable production builds. The worker count and alive task count are fundamental, safe metrics that have no reason to be restricted to unstable builds.

## Expected Behavior

- The ability to query the number of worker threads in a runtime should be available without any unstable feature flags.
- The ability to query the number of currently alive tasks should likewise be available in stable builds.
- Metrics that genuinely require unstable internals should continue to require the unstable flag and should be organized in a dedicated location that makes that distinction clear.

## Why This Matters

Library users who want to observe basic runtime properties (like worker count or task liveness) are currently blocked from doing so in stable builds. Separating stable and unstable metrics also improves the clarity of the API surface — it makes it obvious which metrics are stable and supported versus which are experimental. Moving the unstable tests into their own dedicated file will help maintain this separation going forward.
