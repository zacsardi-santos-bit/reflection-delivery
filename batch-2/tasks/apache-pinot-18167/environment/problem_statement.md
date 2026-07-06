## Description

Apache Pinot currently has no mechanism for plugin or extension code to register custom validation logic that runs automatically before instance or table configurations are committed to the cluster. This means operators cannot enforce enterprise-specific constraints — such as requiring that server instances carry certain pool tags, or that table configurations satisfy multi-tenancy policies — without modifying core Pinot code. Invalid configurations can pass through the standard add/update lifecycle and get written to the cluster's metadata store without ever being checked.

## Expected Behavior

- A pluggable validator registry for instance configurations should exist so that any component can register one or more custom validators. When an instance is added or its configuration is updated, all registered validators are run in the order they were registered before any change is persisted.
- If any registered validator rejects a configuration, the add or update operation must be aborted and the bad configuration must not be written to the cluster. The rejection must be surfaced to the caller.
- The same pluggable pattern should be available for table configurations.
- When updating instance tags specifically, the validator must receive the instance in its final state (with the new tags already applied) before the change is committed.
- A utility conversion must be available to reconstruct a fully typed instance object from its stored cluster representation, so validators and other tooling can inspect typed instance data rather than raw storage records.
- The registries must support resetting (clearing all validators), which is important for test isolation.

## Why This Matters

Without this capability, deployers who need to enforce topology rules or compliance constraints have no clean extension point and must either fork the controller or accept that their constraints are only enforced at a higher layer where it may be too late to prevent a bad write.
