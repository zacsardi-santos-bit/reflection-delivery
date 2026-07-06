## Description

Kafka's tiered storage feature currently copies log segments to remote storage as soon as they roll and become eligible (i.e., their end offset is below the last stable offset). There is no mechanism to delay this upload, which can be undesirable in scenarios where operators want recent data to remain locally accessible before being offloaded to remote storage.

This issue requests the addition of two new configuration options — one time-based and one size-based — that control how long a segment must "age" before it becomes eligible for remote copy.

## Expected Behavior

- A time-based upload delay setting: a segment should not be uploaded until the time elapsed since its most recent record has reached a configurable threshold.
- A size-based upload delay setting: a segment should not be uploaded until enough local data (measured in bytes) has accumulated after it.
- A value of 0 for either setting disables that particular delay check and results in immediate upload (the existing behavior).
- A value of -1 means the setting should resolve to the effective local retention (time or size) as the maximum delay.
- If either delay condition is satisfied (time OR size), the segment becomes eligible for upload.
- Both the topic-level and broker-level variants of these settings must be validated: setting a positive copy lag that exceeds the effective local retention must be rejected with an error at configuration time.
- Both settings must be dynamically reconfigurable at the broker level.

## Why This Matters

Operators running tiered storage workloads need finer control over when data is promoted to remote storage. Uploading too eagerly can consume unnecessary network and storage bandwidth for data that is still actively read locally. Having a configurable delay allows tuning the trade-off between local availability and remote storage utilization.
