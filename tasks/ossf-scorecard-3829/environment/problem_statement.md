## Description

The dependency pinning check in scorecard currently handles both the raw data collection and the score evaluation within the same layer, making it difficult to test and evolve independently. The raw dependency data (whether each dependency is pinned or not, its type, its location) is converted to findings and scored all in one place, without a dedicated probe layer in between.

We want to refactor the pinned-dependencies check to follow the same probe-based architecture used by other scorecard checks. This means introducing a new probe that converts raw dependency data into structured findings, and updating the evaluation function to accept those pre-computed findings rather than raw data.

## Expected Behavior

- A new probe for pins-dependencies should exist, accepting raw check results and converting each dependency into a finding with the appropriate outcome (pinned → positive, unpinned → negative, no data → not available, errors → error outcome, missing info → not applicable).
- The probe should return an identifiable probe name alongside the findings.
- The evaluation function should accept a list of pre-computed findings and a logger, rather than raw dependency data, and compute the score from those findings.
- The top-level check entry point should accept only the check request and orchestrate the full flow.
- Processing errors in the raw data should be surfaced as error-outcome findings from the probe.
- When a dependency has no location and no message, the probe should return an internal error.

## Why This Matters

This change decouples the data-to-finding conversion from the scoring logic, making each layer independently testable and consistent with the rest of the scorecard architecture. It also makes it easier to add or modify dependency pinning behavior in the future without touching multiple intertwined layers.
