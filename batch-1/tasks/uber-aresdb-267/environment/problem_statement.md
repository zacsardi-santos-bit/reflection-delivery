## Description

The bootstrap manager and peer source components in the data node currently have some coupling issues. The peer source is constructed with an explicit logger argument even though all other components use the shared application logger. The bootstrap manager accepts a broad, top-level options bundle when it only needs a narrow set of bootstrap-specific options, creating an unnecessary dependency on unrelated configuration.

Additionally, there is no test coverage for the bootstrap manager's concurrent behavior. When multiple goroutines simultaneously request bootstrapping, the system must not launch a separate bootstrap process for each caller — it should run the current one, queue at most one more, and discard the rest. This deduplication behavior is critical for correctness under load but has never been verified.

## Expected Behavior

- The peer source constructor should no longer require a logger argument. It should use the shared application logger internally.
- The bootstrap manager constructor should accept a focused, bootstrap-specific options type rather than the full top-level options object.
- When multiple concurrent bootstrap requests arrive while a bootstrap is already running, the manager should run at most one additional bootstrap after the current one finishes — not one per caller.
- The bootstrap manager should correctly report whether bootstrapping has completed and the time of the last successful bootstrap.

## Why This Matters

Passing a logger into the peer source is redundant boilerplate that every caller must supply. Accepting a broad options bundle in the bootstrap manager introduces tight coupling between subsystems. The missing concurrency test means a class of correctness bugs (redundant bootstrap executions under load) could go undetected.
