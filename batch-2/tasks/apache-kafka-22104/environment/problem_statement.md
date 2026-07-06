## Description

When using the Kafka configuration command-line tool to delete configuration properties from a topic, broker, or other entity, the tool currently performs an unnecessary preliminary read of the existing configuration before applying any change. This extra round-trip is wasteful, and worse, it causes the operation to fail when a user tries to delete a property that isn't currently set on the resource.

## Expected Behavior

- Deleting a configuration key that does not exist on a resource should succeed silently — it should be treated as a no-op rather than an error.
- Altering configurations should not require a preliminary read of existing configurations before applying the change. The tool should go directly to applying the incremental configuration change.
- This idempotent behavior should work consistently across all entity types: topics, specific brokers, and broker defaults.

## Why This Matters

"Delete this config key if it exists" is a natural and safe operation pattern, especially useful for scripted automation or managing configs on entities that may not all have the same set of properties. Having the tool raise an error when the key doesn't exist makes it fragile and forces users to first check what configs are set before trying to remove any. Removing the preliminary read also reduces unnecessary network overhead.
