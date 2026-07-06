## Description

When a rollout's automated analysis phase fails — because a metric exceeded its failure limit, produced too many inconclusive results, or hit a consecutive error threshold — the system records the terminal phase but stores no explanation of why. Operators see that an analysis run or rollout was aborted, but the status message gives no detail about which metric caused the failure or what threshold was crossed.

This makes it difficult to diagnose rollout failures quickly: engineers must open the individual analysis run resources, look through the metric results, and manually compare counts against configured limits to figure out what went wrong.

## Expected Behavior

- When an analysis metric is assessed as failed, inconclusive, or errored, a human-readable message describing the reason should be generated and attached to the analysis run's status.
- The message should clearly state which metric crossed which threshold (e.g., failure count vs. failure limit, consecutive error count vs. consecutive error limit).
- When a rollout is aborted due to a failed analysis run, that failure message should propagate into the rollout's own status condition so it is immediately visible without inspecting child resources.
- When multiple metrics fail simultaneously, the message from the first (worst) failing metric should be surfaced.
- If the metrics provider itself recorded an error message on the metric result, that should be appended to the failure reason in the rollout status.

## Why This Matters

Rollout failures should be self-describing. Requiring operators to chase down child resources to understand a deployment failure wastes time and increases the blast radius of incidents. With descriptive messages surfaced directly on the rollout status, on-call engineers can understand why a rollout was halted at a glance.
