## Description

Ray Serve's controller tracks health metrics about its operational state, but the timestamp of when the most recent control loop ran is currently missing from those metrics. Operators monitoring a live cluster have no reliable way to determine whether the controller's control loop is actively processing work or has stalled — only that some number of loops have executed, not when the last one happened.

Additionally, the controller health metrics are not currently included in the top-level serve instance details response. Retrieving them requires a separate call to the controller, which is inconvenient for operators and tooling that need a complete snapshot of the cluster state in one request.

## Expected Behavior

- The controller health metrics snapshot should include a field recording the timestamp of the last completed control loop, defaulting to 0.0 when no loop has run yet.
- The health metrics tracker should expose this timestamp as a settable attribute so the controller can update it after each loop.
- When metrics are collected from the tracker, the returned snapshot must reflect the most recently recorded timestamp.
- The controller health metrics should appear as a top-level field in the serve instance details response, so a single API call returns both deployment state and controller health information.
- The health metrics classes should be accessible from the public schema module.

## Why This Matters

Without the last-loop timestamp, it is impossible to distinguish a healthy controller that has run many loops from one that ran a few loops long ago and has since stalled. Surfacing this information directly in the instance details response simplifies monitoring and reduces the number of separate API calls needed to assess cluster health.
