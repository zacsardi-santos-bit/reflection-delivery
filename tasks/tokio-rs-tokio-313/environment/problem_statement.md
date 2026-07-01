# Expose polling status from executor turns and fix fairness issue

## Description

The single-threaded executor's turn-based API currently provides no way for callers to know whether any futures were actually polled during a given turn. This makes it hard to write efficient polling loops, build diagnostics, or determine when the executor is truly quiescent. Callers have to guess or add workarounds to figure out whether real work happened.

Additionally, there is a fairness bug in the executor: futures that are woken up directly (e.g., because one future's completion triggers another via an in-memory channel) are processed in a separate turn from futures woken up via the external reactor or park mechanism. This means futures driven by I/O sources and futures driven by in-memory notifications can be unfairly separated, with one category always getting priority over the other.

## Expected Behavior

- The value returned from each turn of the executor should report whether any futures were polled during that turn.
- When no futures are ready, the turn should report that nothing was polled.
- When futures are ready (either from direct notifications or from external I/O sources), they should all be polled within the same turn — not spread across multiple turns — ensuring fair scheduling.
- After all futures complete and the executor is idle, turns should consistently report that nothing was polled.

## Why This Matters

Without the polling status, callers cannot distinguish productive turns from no-op turns. The fairness issue can cause certain futures to be systematically delayed, which undermines the executor's correctness guarantees for workloads that mix in-memory and I/O-driven futures.
