## Description

Deadline alert configurations attached to workflow definitions need a stable, explicit way to determine whether two alerts represent the same logical configuration. Currently, deadline alerts rely on the standard equality and hashing mechanisms for comparison, but this approach has proven insufficient for two important scenarios:

1. When a workflow is re-processed without any changes to its deadline definitions, the existing deadline alert records in the database should survive unchanged (same IDs). Instead, they are currently being replaced, which breaks continuity.
2. When a deadline alert's timing interval is modified, this change should be detected as a meaningful difference that requires creating a new workflow version with a new hash. Currently this detection may not work correctly.

## Expected Behavior

- Deadline alerts should expose a dedicated method for checking whether one alert matches the definition of another — comparing relevant fields like the timing reference, interval, and callback configuration.
- When two alerts are compared and the argument is not a deadline alert object, the comparison should signal that the comparison is not applicable, rather than simply returning false.
- When a workflow with deadline alerts is re-serialized without changes, the same database records for those alerts must persist with their original identifiers.
- When a deadline alert's interval is changed, re-writing the workflow must produce a new serialized workflow version with a different hash, and the associated alert record must store the updated interval.

## Why This Matters

Without stable deadline alert persistence and proper change detection, the scheduler cannot reliably track which deadline configurations apply to a given workflow version, leading to missed deadlines or incorrect alert assignments across workflow runs.
