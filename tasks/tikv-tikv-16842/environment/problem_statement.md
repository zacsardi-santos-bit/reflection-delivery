## Description

There is a monitoring metric in the raft store layer that tracks regions based on a feature that allows applying log entries before they are fully persisted to disk. The existing metric was counting in the wrong direction — it tracked regions where this feature was **disabled**, rather than where it was **enabled**. This makes the metric semantically confusing and counterintuitive.

## Expected Behavior

- The metric should count how many regions currently have the fast-path apply feature **enabled**, not how many have it disabled.
- When a region starts actively using the feature, the metric count should go up.
- When a region is destroyed (for example, when two regions merge into one), the metric count should go down, accurately reflecting fewer regions using the feature.
- The metric value should be consistent with the actual number of eligible regions at any point in time: after cluster startup with appropriate limits configured, after region splits, and after region merges.

## Why This Matters

Operators monitoring a live cluster rely on this metric to understand how many regions are benefiting from the fast-path apply optimization. With the inverted logic, the metric provides misleading data — showing high numbers when few regions use the feature, and low numbers when the feature is widely adopted. Fixing this ensures the metric directly and intuitively reflects feature usage across region lifecycle events.
