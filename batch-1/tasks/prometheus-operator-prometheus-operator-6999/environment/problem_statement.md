## Description

Telegram supports organizing messages within groups into "topics" (also called threads). Currently, the alertmanager receiver configuration for Telegram has no way to target a specific group topic when sending alerts — all messages go to the main group chat. Users who organize their Telegram groups using topics cannot route alerts to the relevant topic.

Additionally, the labels map type used in static scrape configuration has a type mismatch that causes compile errors in the end-to-end tests.

## Expected Behavior

- Telegram receiver configurations should support an optional group topic ID field, allowing alerts to be delivered to a specific Telegram group thread.
- This field is only supported in newer versions of Alertmanager. When a configuration includes this field but is applied against an older Alertmanager version, the field should be silently dropped with a warning, not cause an error.
- The labels map in static scrape config definitions should use plain string keys.

## Why This Matters

Operators managing large-scale environments often use Telegram group topics to organize alerts by service, team, or severity. Without the ability to specify a topic ID, all alerts go to the same general group chat, making it difficult to separate concerns. This change allows proper routing of alert notifications within structured Telegram groups, while remaining backward-compatible with older Alertmanager deployments.
