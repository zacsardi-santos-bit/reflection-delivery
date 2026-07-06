## Description

The analytics system records guardian review events — structured records of a security review component that approves or denies AI-initiated actions. Some fields on these records are currently required even when they do not logically apply in all situations.

Specifically, the field that identifies the particular item being reviewed is currently mandatory. However, when a review is triggered by a delegated subagent rather than a direct user-facing action, there may be no specific target item to reference. Forcing this field to always be present prevents review events from being recorded in these cases.

Similarly, the count of tool calls made during a review is currently required, but this count is not always known or applicable at the time the review event is emitted.

## Expected Behavior

- The field identifying the item being reviewed should be optional, allowing review events to be created without providing a value when the information is not available.
- The field recording the number of tool calls during a review should also be optional.
- The description of a network access action under review should only include the network protocol and port — it should not include additional fields (such as target host or URL string) that are now tracked elsewhere.

## Why This Matters

Guardian review events need to be recorded across a wider variety of scenarios. Making these fields optional removes an artificial constraint that causes failures when the data is genuinely absent, without degrading the accuracy of events where the data is present.
