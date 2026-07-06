## Description

The dashboard is missing support for a new category of model deployments (NIM-based inference services). These deployments have their own set of performance metrics that should be visualized in the metrics tab, but none of the necessary infrastructure exists yet — there is no way to validate the metrics configuration format specific to these deployments, and no constants exist to identify the different chart types they require.

Additionally, two existing bugs affect integration application management:

1. When an integration application reports an error status, the affected component disappears from the dashboard instead of remaining visible with the error surfaced. Users lose visibility into which apps have problems.

2. When enabling an integration application fails with an API error, the error message is not passed through correctly — the user sees nothing useful instead of the actual failure reason.

## Expected Behavior

- A validation utility should exist to confirm whether a NIM metrics configuration object is well-formed (contains a non-empty list of graph definitions).
- A set of named constants should identify the supported graph types for NIM model metrics.
- When an integration application's status check returns an error, the component should stay in the component list with its enabled state set to false and the error message recorded on the component.
- When integration application enablement fails, the error message from the underlying failure should be correctly captured and reported.
- The integration component watcher should refresh its status when a force-update signal is dispatched to the application state.

## Why This Matters

Without these changes, NIM model metrics cannot be rendered (the configuration cannot be parsed or validated), error states in integration apps silently remove components rather than surfacing the issue, and failed enablement attempts give users no feedback about what went wrong.
