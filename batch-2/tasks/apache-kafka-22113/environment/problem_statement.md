## Description

The Kafka configuration management tool performs an unnecessary read operation before applying configuration changes. Every time a user runs an alter command, the tool fetches the current configuration from the broker before submitting the actual change, even though this pre-read serves no functional purpose for incremental alter operations. This adds extra latency and an unnecessary network round trip to every config change.

Additionally, the tool currently throws an error when a user tries to delete a configuration key that doesn't exist on a resource. This makes it impossible to write idempotent configuration management scripts — for example, ensuring that a particular setting is absent on a topic or broker should succeed even if the key was never set in the first place.

## Expected Behavior

- Altering configurations (for topics, brokers, default brokers, client metrics, groups) should not require a pre-read of the current configuration — the tool should apply the incremental change directly.
- Deleting a configuration key that does not exist on the target resource (topic, named broker, or default broker) should succeed without throwing an error. The operation should be treated as a no-op success.

## Why This Matters

Removing the unnecessary pre-read reduces latency and broker load for every alter operation. Making deletion of non-existent configuration keys idempotent enables safe, repeatable automation scripts that ensure certain settings are absent — without needing to first check whether the key exists.
